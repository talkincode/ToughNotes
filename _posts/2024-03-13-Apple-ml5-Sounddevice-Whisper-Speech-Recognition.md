---
layout: post
title:  "Apple mlx框架及Sounddevice搭档Whisper, 实现语音实时识别"
date:   2024-03-13 09:19:11 +0800
categories: speech-recognition mlx whisper Sounddevice
keywords: speech-recognition，mlx，whisper，Sounddevice
---

在本文中，我们将探索通过结合Apple的mlx框架和Sounddevice库与OpenAI创造的Whisper模型，如何实现实时语音识别的功能。我们将看到这一过程的核心代码，并附上详细解释和注释。这样的技术可以应用于各种语音到文本场景中，如自动字幕生成、语音助手、实时会议记录等。

> 注意，这里使用的 whisper 并非 openai-whisper， 而是来自 Apple 封装厚的 [whisper](https://github.com/ml-explore/mlx-examples)
>
> 为什么没有使用 pyaudio： 在使用 pyaudio 时遇到了一点小问题， pyaudio 对 apple arm芯片支持的不够完美，很容易出错

## 依赖的 模块

```bash
numba
numpy
torch
tqdm
more-itertools
tiktoken==0.3.3
huggingface_hub
mlx
pyaudio
scipy
```

## mlx 模型转换

convert.py 来自 [mlx-examples](https://github.com/ml-explore/mlx-examples/tree/main/whisper)

```bash
model="medium"
python convert.py --torch-name-or-path ${model} --mlx-path mlx_models/${model}_fp16
python convert.py --torch-name-or-path ${model} --dtype float32 --mlx-path mlx_models/${model}_fp32
python convert.py --torch-name-or-path ${model} -q --q_bits 4 --mlx-path mlx_models/${model}_quantized_4bits

```

## 范例代码

```python
# 导入必要的库
import numpy as np
import sounddevice as sd
import whisper
import threading

# 定义一个AudioTranscriber类
class AudioTranscriber:
    # 构造函数初始化模型路径及采样率
    def __init__(self, model_path="mlx_models/medium_fp16"):
        self.model_path = model_path
        self.sample_rate = 16000  # 设置采样率为16000Hz
        self.silence_threshold = 0.01  # 设置静音阈值，判断时否为静音

    # 定义音频转录功能
    def transcribe(self, audio_data):
        # 使用Whisper模型转录音频数据
        result = whisper.transcribe(audio_data, path_or_hf_repo=self.model_path)
        print("Transcription: ", result['text'])  # 打印转录结果

    # 定义音频回调函数
    def audio_callback(self, indata, frames, time, status):
        if status:
            print(status, file=sys.stderr)
        # 转换音频数据格式以适应模型
        audio_data = np.squeeze(indata).astype(np.float32)
        # 检测静音
        if np.mean(np.abs(audio_data)) > self.silence_threshold:
            # 创建新线程进行转录
            threading.Thread(target=self.transcribe, args=(audio_data,)).start()

    # 开始监听并转录音频
    def start_listening(self, duration=5):
        # 以指定的采样率打开音频输入流
        with sd.InputStream(callback=self.audio_callback,
                            samplerate=self.sample_rate,
                            channels=1,
                            dtype='float32',
                            blocksize=int(self.sample_rate * duration)):
            print("Listening, please speak now...")
            # 监听一段时间后停止
            sd.sleep(duration * 1000)

# 主函数
if __name__ == "__main__":
    transcriber = AudioTranscriber()  # 创建AudioTranscriber实例
    transcriber.start_listening(10)  # 开始监听10秒的音频
```

以下是对上述核心代码的详细解释和注释：

1. **初始化Transcriber实例**：
   - `__init__`函数设置了模型路径，采样率，和静音阈值。
   - `self.model_path`用于定位存储Whisper模型的目录。
   - `self.sample_rate`设定了录音的采样率为16000Hz, 这是Whisper模型要求的标准采样率。
   - `self.silence_threshold`用于检测音频是否静音（音量过低）。

2. **实时音频转录**：
   - `transcribe`函数调用whisper库，将实时捕获的音频数据转录成文本。
   - `whisper.transcribe`函数负责执行音频转录工作。
   - 输出转录结果的文本。

3. **处理音频流**：
   - `audio_callback`是一个回调函数，由sounddevice库在捕获到音频帧时调用。
   - 检查是否有错误信息，如果有，会输出到标准错误流。
   - 把捕获的音频数据`indata`压缩和转换成单通道浮点数据，为模型处理做准备。
   - 如果音频数据超出设定的静音阈值，则启动一个线程，调用`transcribe`进行实时转录。

4. **开始监听**：
   - `start_listening`函数使用sounddevice库开启一个音频输入流进行监听。
   - 音频流配置采样率为16000Hz，单通道，32位浮点数格式。`blocksize`定义了每次处理的数据块大小。
   - 使用`sd.sleep`函数来控制监听的持续时间。

在上述代码的支持下，我们可以构建一个可以实时监听用户语音，并将其转录为文本的应用程序。通过多线程技术，我们保证了语音实时识别的流畅性和效率。此外，静音检测功能避免了无效数据的处理，提升了整体性能。在实践中，以上代码可以集成到各种支持Python的环境下，为用户提供即时的语音识别服务。

不过缺点也是存在的，使用 threading 不是个好主意， 这个实时依然依赖于硬件的能力， 即使在满血 MacStudio 上依然有3-5秒的延时。

## 另一个简单的例子

这个例子简单的实现了一次识别

```python
import numpy as np
import sounddevice as sd
import whisper

class AudioTranscriber:
    def __init__(self, model_path="mlx_models/medium_fp16"):
        self.model_path = model_path
        self.sample_rate = 16000 
        
    def transcribe(self, duration=5):
        print("Recording...")
        indata = sd.rec(int(duration * self.sample_rate), samplerate=self.sample_rate, channels=1, dtype='float32')
        sd.wait() 
        print("Recording stopped.")
        audio_data = np.squeeze(indata).astype(np.float32)
        result = whisper.transcribe(audio_data, path_or_hf_repo=self.model_path)
        print("Transcription: ", result['text'])


if __name__ == "__main__":
    transcriber = AudioTranscriber()
    transcriber.transcribe(5) 
```
