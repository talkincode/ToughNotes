---
layout: post
title:  "Sounddevice: 高效的Python音频处理库"
date:   2024-03-11 20:59:05 +0800
categories: python audio sounddevice
keywords: python, audio, sounddevice
---

Sounddevice 是一个用于录制和播放音频的Python库，它以直观的API为特色，并为PortAudio音频I/O库提供了一个封装。Sounddevice是 cross-platform，支持多种操作系统和硬件设备。以下是一些比较常见的例子：

- 实时音频处理：利用 `sounddevice.Stream` 对象，可以实现低延迟的实时音频信号处理。音乐家和音频工程师可能使用 Sounddevice 来处理实时音乐表演的音频信号，比如实时混音、音频特效的添加和态音频信号监测。

- 音频录制：Sounddevice 可用于录制音频信号，例如录制演讲、会议内容或者音乐创作时的素材。例如，可以使用 `sounddevice.InputStream` 类来监听音频输入设备，并将接收到的音频数据保存到文件。

- 音频播放：与音频录制相对，Sounddevice 也可以用于播放音频文件。例如，在播放器或音乐应用程序中，可以利用 `sounddevice.OutputStream` 来播放音乐和声效。

- 实验和教育：在教育和研究场景中，Sounddevice 作为一个易于使用的包，允许学生和研究人员探索数字信号处理的概念。例如，在数字音频处理的课程中，可以利用它来演示滤波器、FFT变换和其他信号处理方法的效果。

- 音频分析：Sounddevice 可以用于音频信号的分析，比如声音识别、频谱分析或者音乐信息检索。例如，可以从输入流获取音频数据，使用信号处理技术进行分析，并提取需要的信息，如节拍、音高或声音特征。

- 输入/输出流路由：对于需要定制音频流路由的复杂应用程式，Sounddevice 可以提供高级的输入/输出流管理。比如一个虚拟音频合成器可能需要将多个输入信号路由到不同效果处理链中，然后再混合输出。

- 人机交互：在需要音频作为交互媒介的应用中，如语音指令识别或音频反馈，Sounddevice 可以实现音频的捕获和输出。

## 1. 初始化与终止

当`sounddevice`模块被导入时，PortAudio系统会自动被初始化。在大多数情况下，用户无需手动初始化或终止。如果需要，可以使用`sounddevice._initialize()`和`sounddevice._terminate()`来手动管理。

在使用 `sounddevice` 模块进行音频处理时，通常不需要显式初始化和终止 PortAudio 系统，因为这会在模块导入时自动完成。但是，如果需要直接控制初始化和终止过程，可以调用 `sounddevice` 模块的私有函数 `_initialize()` 和 `_terminate()`，如以下示例所示：

## 2. 核心对象和函数

### 2.1. Stream 对象

`sounddevice.Stream`: 一个全双工的音频流。通过实例化这个类可以同时处理音频的播放和录音。该对象提供了开始(start)、停止(stop)、中止(abort)和关闭(close)流的方法。下面列举了创建 `Stream` 对象时可以使用的一些参数以及它们的作用：

- `samplerate`：采样率，以赫兹（Hz）为单位，确定每秒钟采集和播放的样本数。标准的CD质量采样率是44100Hz。

- `blocksize`：块大小，定义了传递给 stream 回调函数的帧数，或阻塞型读/写流的首选块粒度。0 表示使用主机 API 的最佳块大小。

- `device`：设备索引或查询字符串，指定要使用的输入/输出设备。如果未指定，默认从 `sounddevice.default.device` 中获取设置。

- `channels`：通道数，定义了要传递给流回调函数的声音通道数，或者 `read()`/`write()` 访问的通道数。

- `dtype`：数据类型，指定了提供给流回调函数、`read()` 或 `write()` 的 numpy 数组的样本格式。支持的格式有 'float32'、'int32'、'int16'、'int8'、'uint8' 等。

- `latency`：延迟，以秒为单位，表示期望的流延迟时间。`'low'` 和 `'high'` 分别代表设备的默认低延迟和高延迟配置。

- `extra_settings`：特定于主机 API 的额外设置，如 ASIO 的通道选择等。

- `callback`：用户提供的回调函数，用于在流活动期间生成或处理音频数据。回调函数的参数定义了输入和输出缓冲区，帧数，回调时间和状态。

- `finished_callback`：用户提供的回调函数，当流变为非活动状态时调用。

- `clip_off`：布尔值，设置为 `True` 以禁用默认的剪裁。

- `dither_off`：布尔值，设置为 `True` 以禁用默认的抖动。

- `never_drop_input`：布尔值，对于全双工流，设置为 `True` 可请求不丢弃溢出的输入样本。

- `prime_output_buffers_using_stream_callback`：布尔值，设置为 `True` 会在初始输出缓冲区上调用流回调进行填充，而不是使用默认的 0 填充。

这些参数允许在创建 `Stream` 对象时进行精细控制，优化音频流的行为以匹配特定应用程序的需求。

以下是使用 `sounddevice.Stream` 对象的代码示例：

```python
import sounddevice as sd
import numpy as np

# 采样率
fs = 44100
# 持续时间（秒）
duration = 5

# 回调函数
def callback(indata, outdata, frames, time, status):
    if status:
        print(status)
    # 在此处处理音频数据
    outdata[:] = indata  # 直接将输入数据复制到输出数据中，形成回声

# 打开音频流
with sd.Stream(callback=callback, samplerate=fs, channels=2) as stream:
    # 准备开始录制五秒音频
    sd.sleep(int(duration * 1000))  # 暂停主线程，允许音频流处理音频数据
```

在这个例子中，我们定义了一个采样率为 44.1 kHz 的音频流，双声道输入和输出。`callback` 函数用于处理音频数据，在本例中，它简单地将接收到的输入数据复制到输出数据中，这将导致音频信号被回放（回声效果）。

注意，通过使用 Python 的 `with` 语句，我们保证音频流在退出代码块时被正确关闭。我们在 `with` 代码块中使用 `sd.sleep()` 函数来避免主线程终止，在这段时间内 callback 函数会不断地被调用并处理音频数据。

请根据实际应用调整回调函数 `callback` 中音频处理的逻辑。

### 2.2. 输入/输出流

#### 2.2.1. `sounddevice.InputStream`: 专门用于录音的流

`sounddevice.InputStream` 是用于录音的流对象。您可以创建一个 `InputStream` 实例来接收音频数据，这些数据可以用于实时处理或保存至文件。以下是 `InputStream` 类的构造函数参数的详细解释：

- `samplerate` (可选): 以赫兹（Hz）为单位的采样率，确定每秒钟采集的样本数。标准CD质量是44100Hz。如果未指定，默认使用 `sounddevice.default.samplerate` 的设置。

- `blocksize` (可选): 块大小，定义了传递给 stream 回调函数的帧数。 `0` 表示使用主机 API 的最佳块大小。如果未指定，默认使用 `sounddevice.default.blocksize` 的设置。

- `device` (可选): 设备索引或查询字符串，指定要使用的音频输入设备。如果未指定，默认从 `sounddevice.default.device` 中获取输入设备的设置。

- `channels` (可选): 音频通道数，定义了要传递给 stream 设备的声道数量。如果未指定，默认使用 `sounddevice.default.channels` 中的输入通道数设置。

- `dtype` (可选): 数据类型，指定了为录音数据指定的 NumPy 数组的数据类型。支持的格式包括 'float32'、'int32'、'int16'、'int8' 和 'uint8'。如果未指定，默认使用 `sounddevice.default.dtype` 中的输入数据类型。

- `latency` (可选): 延迟，以秒为单位，表示流的期望延迟时间。值可以是具体的延迟时间，或字符串 `'low'` 和 `'high'`，分别代表设备的默认低延迟和高延迟配置。如果未指定，默认使用 `sounddevice.default.latency` 中的输入设备延迟。

- `extra_settings` (可选): 特定于主机 API 的额外设置，例如使用 ASIO 驱动时可指定的额外设置如 `sounddevice.AsioSettings`。

- `callback` (可选): 用户提供的回调函数，用于在流活动期间处理音频数据。回调函数接收几个参数：输入数据、帧数、时间信息以及状态，它必须返回 `None`。

- `finished_callback` (可选): 用户提供的回调函数，当流变为非活动状态时调用。例如，可以用于清理在录音过程中使用的资源。

- `clip_off` (可选): 布尔值，如果设置为 `True`，将关闭默认的剪裁功能，否则超出范围的样本将被剪裁为有效的最大/最小值。

- `dither_off` (可选): 布尔值，如果设置为 `True`，将关闭默认的抖动功能。

- `never_drop_input` (可选): 布尔值，对于全双工流，将此项设为 `True` 可以保证不会丢弃溢出的输入样本。

- `prime_output_buffers_using_stream_callback` (可选): 布尔值，如果设为 `True`，流的输出缓冲区会在开始时使用回调函数来填充，而不是默认的零填充。

创建一个 `InputStream` 实例用于录制音频，并打印状态消息：

```python
import sounddevice as sd

# 回调函数处理录音数据
def callback(indata, frames, time, status):
    if status:
        print(status)

# 创建 InputStream 对象
stream = sd.InputStream(callback=callback)

# 开始录音
with stream:
    sd.sleep(5000)  # 录音 5 秒钟
```

在这个例子中，回调函数仅检查并打印状态信息，但实际中您可能需要将输入数据 `indata` 写入文件或进行进一步处理。通过使用 Python 的 `with` 语句，我们确保在代码块结束时 `InputStream` 会被正确关闭，并且通过 `sd.sleep()` 暂停主线程，允许音频流在此期间处理音频数据。

#### 2.2.2. `sounddevice.OutputStream`: 专门用于播放音频的流

在 `sounddevice` 库中，`OutputStream` 类用于创建一个只支持音频播放的流。下面是它的参数的详细解释和使用方法：

`OutputStream`类的构造函数接收以下参数：

- `samplerate` (可选): 采样率，以赫兹（Hz）为单位。这是每秒钟捕获和播放的样本数。如果未指定，默认使用 `sounddevice.default.samplerate` 的设置。

- `blocksize` (可选): 块大小，定义了传递给流的回调函数的帧数。 `0` 表示使用主机 API 的最佳块大小。如果未指定，默认使用 `sounddevice.default.blocksize` 的设置。

- `device` (可选): 设备索引或查询字符串，指定要使用的音频输出设备。如果未指定，默认从 `sounddevice.default.device` 中获取输出设备的设置。

- `channels` (可选): 音频通道数，定义了要传递给流的声道数量。如果未指定，默认使用 `sounddevice.default.channels` 中的输出通道数设置。

- `dtype` (可选): 数据类型，指定了为播放数据指定的 NumPy 数组的数据类型。支持的格式包括 'float32'、'int32'、'int16'、'int8' 和 'uint8'。如果未指定，默认使用 `sounddevice.default.dtype` 中的输出数据类型。

- `latency` (可选): 延迟，以秒为单位，表示流的期望延迟时间。值可以是具体的延迟时间，或字符串 `'low'` 和 `'high'`，分别代表设备的默认低延迟和高延迟配置。如果未指定，默认使用 `sounddevice.default.latency` 中的输出设备延迟。

- `extra_settings` (可选): 特定于主机 API 的额外设置，例如使用 ASIO 驱动时可指定的额外设置如 `sounddevice.AsioSettings`。

- `callback` (可选): 用户提供的回调函数，用于在流活动期间生成音频数据。回调函数接收几个参数：输出数据、帧数、时间信息以及状态，它必须返回 `None`。

- `finished_callback` (可选): 用户提供的回调函数，当流变为非活动状态时调用。例如，可以用于清理在播放过程中使用的资源。

- `clip_off` (可选): 布尔值，如果设置为 `True`，将关闭默认的剪裁功能，否则超出范围的样本将被剪裁为有效的最大/最小值。

- `dither_off` (可选): 布尔值，如果设置为 `True`，将关闭默认的抖动功能。

- `never_drop_input` (可选): 布尔值，对于全双工流，将此项设为 `True` 可以保证不会丢弃溢出的输入样本。

- `prime_output_buffers_using_stream_callback` (可选): 布尔值，如果设为 `True`，流的输出缓冲区会在开始时使用回调函数来填充，而不是默认的零填充。

具体使用时，需要根据自己的需求配置参数。以下演示一个创建 `OutputStream` 对象并播放一段随机生成的声音的示例：

```python
import sounddevice as sd
import numpy as np

# 生成一秒钟的随机噪音
fs = 44100  # 采样率
data = np.random.uniform(-1, 1, fs)

# 创建输出流
stream = sd.OutputStream(samplerate=fs, channels=1)

# 启动输出流并播放数据
with stream:
    stream.write(data)
```

在这个示例中，我们首先生成了随机音频数据，然后创建了一个 `OutputStream` 对象进行播放。需要注意的是，`OutputStream` 类的实例也可以作为上下文管理器使用，在 `with` 语句块结束时自动关闭流。

### 2.3. 播放与录制函数

#### 2.3.1. `sounddevice.play()`: 用于播放NumPy数组中的音频信号

`sounddevice.play()` 函数是用于通过Python的`sounddevice`库播放NumPy数组中的音频数据的便捷函数。它简化了音频播放的过程，让用户不必手动创建和管理音频流。下面将详细解释该函数的参数以及其用途：

- **data** (`array_like`): 包含音频数据的数组。如果数组是二维的，每一列都表示一个通道；如果是一维数组，则视为单声道数据。此参数是必需的。
  
- **samplerate** (`float`, 可选): 音频的采样率，以赫兹（Hz）为单位。如果未指定，则使用默认设备的采样率。此参数对音频的播放速度和音调都有影响。
  
- **mapping** (`array_like`, 可选): 一个列表，用于指定哪些通道用于播放`data`中的音频。列表中的每个数字对应一个通道编号（编号从1开始）。如果未指定，则音频数据将被播放到默认的输出设备通道上。

- **blocking** (`bool`, 可选): 如果为`True`，`play()` 调用将阻塞，直到全部数据播放完毕。如果为`False`（默认值），将在后台进行播放，并且函数会立即返回。非阻塞调用可以随时使用`sounddevice.stop()`停止。

- **loop** (`bool`, 可选): 如果为`True`，音频数据将循环播放，直到明确停止。

- **\*\*kwargs**: 其他传递给`OutputStream`的关键字参数。

`play()`函数没有返回值。如果在播放时发生错误，则会抛出`sounddevice.PortAudioError`异常。

播放一个440 Hz的正弦波:

```python
import sounddevice as sd
import numpy as np

fs = 44100  # 采样率
f = 440     # 频率, Hz
seconds = 1 # 持续时间秒
t = np.linspace(0, seconds, int(fs*seconds), endpoint=False)
data = np.sin(2 * np.pi * f * t)
sd.play(data, samplerate=fs)
sd.wait()  # 等待直到数据播放完成
```

上面的例子创建了一个时长为1秒的正弦波信号，并用采样率为44100 Hz播放它。在播放后，代码使用`sd.wait()`保持程序阻塞，直到播放结束。

注意：使用`sounddevice.play()`时应该确保你的NumPy数组中的数据类型与声音设备可接受的数据类型匹配。通常，一种安全的选择是使用`float32`数据类型，其取值范围为`-1.0`到`1.0`。

#### 2.3.2. `sounddevice.rec()`: 用于录制音频并将结果存储在NumPy数组中

函数 `sounddevice.rec()` 用于录制音频并将结果存储在NumPy数组中。以下是`sounddevice.rec()`的参数详解：

- `frames`: 录音帧数。一帧对应于所有通道的单个样本集合。例如，若要录制5秒的音频，并且采样率（samplerate）是44100，那么frames将是`5*44100`。

- `samplerate`: 采样率，以赫兹（Hz）为单位。这是每秒钟捕获和播放的样本数。常见的采样率包括44100（CD质量）、48000（专业音频设备）等。

- `channels`: 录制的通道数。单声道为1，立体声为2。

- `dtype`: 指定录制数据的数据类型。常用类型包括`'float32'`和`'int16'`。`'float32'`将样本值范围规范化为从-1.0到+1.0，而`'int16'`是16位整型，表示的范围则是-32768到+32767。

- `out`: 可选。输出的NumPy数组。如果指定了`out`参数，则将在该数组中放置录制的音频数据，否则内部会创建一个新数组。

- `mapping`: 可选。这是一个指定哪些通道（从1开始编号）实际用于录音的序列。通常情况下，默认的设备所有通道都会被使用。（此参数并未在原文的注释中提到，但在函数API中存在。）

- `blocking`: 可选。默认值为`False`。如果设置为`True`，函数调用将阻塞，直到录制完成。否则，它会立即返回，并且需要外部逻辑来确保录制完成，通常是通过`sounddevice.wait()`函数。

- `**kwargs`: 其他一些可选的关键字参数，可能在未来版本的sounddevice模块中添加。

示例用法：

```python
import sounddevice as sd

fs = 44100  # 采样率
duration = 5  # 持续时间（秒）

# 录制
myrecording = sd.rec(int(duration * fs), samplerate=fs, channels=2, dtype='float32')
sd.wait()

# myrecording 现在包含了录制的音频数据
```

这将录制5秒钟的立体声音频，并在NumPy数组`myrecording`中以32位浮点格式保存样本数据。通过`sd.wait()`确保录音完成，然后可以进一步处理或保存录音数据。

#### 2.3.3. `sounddevice.playrec()`: 同时录制和播放音频

函数 `sounddevice.playrec()` 用于同时进行音频的播放和录制，并将播放的音频和录制的音频结果作为 NumPy 数组返回。

`sounddevice.playrec()` 是一个高度灵活的函数，它结合了音频播放和录制功能。在实时音频处理中，这个函数非常有用。以下是一些典型应用场景：

1. **回声消除测试**：
   开发音频处理中的回声消除算法时，可以使用 `playrec()` 播放一段音频并同时录制麦克风输入，以便分析回声效果并优化算法。

2. **实时音频效果处理**：
   对于音乐制作者和声音工程师而言，`playrec()` 可以实现实时音效处理，比如实时添加混响、均衡器设定或其他音频效果。

3. **乐器调音软件**：
   乐器练习时可以通过 `playrec()` 获取乐器声音输入，并实时提供调音反馈，从而辅助乐器的准确调音。

4. **音频同步应用**：
   在音视频开发中，`playrec()` 可以在播放音轨的同时录制声音，帮助开发者实现音频与视频的精确同步。

5. **双向通讯应用**：
   在VoIP通讯或语音会议系统中，`playrec()` 可以同时处理语音输入和输出流，实现如电话会议等双向通信功能。

6. **声音识别测试**：
   使用 `playrec()` 进行声音识别训练时，可以在播放特定声音的同时录制环境中的声音响应，用于声音识别算法的训练和测试。

7. **声学测量**：
   在声学测量和分析中，通过 `playrec()` 播放已知测试信号并录制响应，以测量房间的声学特性或扬声器的频率响应。

8. **教育与研究**：
   教育和研究领域中，`playrec()` 可以用来演示和探讨声音的录制和播放原理，以及数字信号处理的实时应用案例。

`playrec()` 由于其实时性和便捷性，成为了音频处理应用程序中不可或缺的函数之一。它使应用程序能够同时控制音频的输入和输出，从而支持多种复杂的音频处理流程。

以下是`sounddevice.playrec()`的参数详解：

- `data`: array_like
  要播放的音频数据的数组。如果数组是二维的，每一列都表示一个通道；如果是一维数组，则视为单声道数据。这个参数是必需的。

- `samplerate`: float, 可选
  音频的采样率，以赫兹（Hz）为单位。如果未指定，则使用默认设备的采样率。

- `channels`: int, 可选
  录制的通道数。单声道为1，立体声为2。

- `dtype`: str 或 numpy.dtype, 可选
  指定录制数据的数据类型。常用类型包括 `'float32'` 和 `'int16'`。`'float32'` 将样本值范围规范化为从-1.0到+1.0，而 `'int16'` 是16位整型，表示的范围则是-32768到+32767。

- `out`: numpy.ndarray 或子类, 可选
  如果指定了 `out` 参数，则将在该数组中放置录制的音频数据，否则内部会创建一个新数组。

- `mapping`: array_like, 可选
  这是一个指定哪些通道（从1开始编号）实际用于播放和录音的序列。通常情况下，默认的设备所有通道都会被使用。

- `blocking`: bool, 可选
  默认值为 `False`。如果设置为 `True`，函数调用将阻塞，直到录制完成。否则，它会立即返回，并且需要外部逻辑来确保录制完成，通常是通过 `sounddevice.wait()` 函数。

- `**kwargs`: 其他一些可选的关键字参数。

示例用法：

```python
import sounddevice as sd
import numpy as np

# 创建音频数据
fs = 44100  # 采样率
duration = 5  # 持续时间（秒）
frequency = 440  # 频率（Hz）
samples = (np.sin(2 * np.pi * np.arange(fs * duration) * frequency / fs)).astype(np.float32)

# 播放并录制音频
myrecording = sd.playrec(samples, samplerate=fs, channels=2, dtype='float32')
sd.wait()  # 等待直到数据播放和录制完成
```

在这个例子中，我们首先生成一个时长为5秒，频率为440 Hz的正弦波音频数据，并用采样率为44100 Hz的设定将其播放出去。同时，我们还录制了音频，保存在 `myrecording` 这个 NumPy 数组中。播放和录制完成后，使用 `sd.wait()` 函数来确保操作完成。

### 2.4. 查询函数

#### 2.4.1. `sounddevice.query_devices()`: 查询可用的音频输入/输出设备

`sounddevice.query_devices()` 是一个函数，它查询可用的音频输入/输出设备，并返回有关这些设备的信息。这个函数可以在不带参数的情况下调用，也可以接受一些可选参数来查询特定的设备或者设备的特定类型（输入或输出）。返回的结果取决于所传递的参数。

- `device` (可选): 可以是一个设备的数字ID或者设备名称的字符串。如果指定了这个参数，`query_devices()` 只会返回有关这个特定设备的信息。如果不指定或为None，则返回所有可用设备的信息。

- `kind` (可选): 可以是 `'input'` 或 `'output'`。如果指定了 `kind` 而没有指定 `device`，则函数返回有关默认输入或输出设备的信息。如果 `device` 也被指定，`kind` 参数将决定要查询输入还是输出属性（如果设备同时支持输入和输出）。

当不带任何参数调用时，返回一个包含所有可用设备信息的 `sounddevice.DeviceList` 对象，其中每个设备的信息用一个字典表示，键包括设备的名称、索引号、所属的主机API、最大输入/输出通道数、默认低延迟和高延迟值以及默认采样频率等。

如果 `device` 或 `kind` 被指定，则返回一个含有所请求设备信息的字典。

查询所有可用设备：

```python
import sounddevice as sd
devices = sd.query_devices()
print(devices)
```

查询特定设备的信息：

```python
device_info = sd.query_devices(device=2)  # 查询设备ID为2的设备
print(device_info)
```

查询默认输出设备的信息：

```python
default_output_device = sd.query_devices(kind='output')
print(default_output_device)
```

查询默认输入设备的信息：

```python
default_input_device = sd.query_devices(kind='input')
print(default_input_device)
```

在实际使用 `query_devices()` 时，通常需要查看设备的特性（例如支持的通道数或者默认延迟），或者在打开音频流之前选择合适的设备。此外，这个函数还可以在音频设备调试和选择最佳设备时提供有用的信息。

#### 2.4.2. `sounddevice.query_hostapis()`: 查询可用的音频主机API信息

`sounddevice.query_hostapis()` 函数用于查询计算机上可用的音频主机API。在PortAudio中，主机API是具体的音频接口，比如ASIO, DirectSound, WASAPI (在Windows上) 或者 ALSA, OSS, JACK (在Linux上)。此函数用来获取有关这些音频主机API的信息，这对于了解系统配置和调试音频问题非常有用。

`sounddevice.query_hostapis()`函数只接受一个可选参数：

- `index`: int, 可选
  - 若指定，将仅返回关于给定主机API *index* 的信息，呈现为一个字典。

函数的返回值取决于是否指定了 *index* 参数：

- 若未指定 `index`，函数返回一个包含每个可用主机API信息的`tuple`，其中每个API的信息都以字典形式存在。
- 若指定 `index`，将返回一个字典，包含指定主机API的信息。

每个API信息的字典都包含以下键：

- `'name'`: 主机API的名称。
- `'devices'`: 属于此主机API的设备ID列表。使用 `query_devices()` 获取有关设备的信息。
- `'default_input_device'`, `'default_output_device'`: 对应于每个主机API的默认输入/输出设备ID。若不存在默认输入/输出设备，则值为-1。

这些信息可以帮助您选择合适的主机API和设备来打开音频流。

查询所有可用的音频主机API:

```python
import sounddevice as sd

hostapis = sd.query_hostapis()
print(hostapis)
```

查询特定主机API的信息：

```python
index = 0  # 假设我们想查询第一个主机API的信息
hostapi_info = sd.query_hostapis(index)
print(hostapi_info)
```

获取默认输入设备的主机API：

```python
default_hostapi = sd.query_hostapis(sd.default.hostapi)
print(default_hostapi)
```

这些示例显示了如何使用`sounddevice.query_hostapis()`函数获取主机API的详细信息。这些信息在配置音频硬件接口和调试音频流时非常有用。

### 2.5. 默认设备和设置

- `sounddevice.default.device`: 默认音频设备的ID。

`sounddevice.default.device` 是一个属性，它被用来存储和读取默认的输入和输出音频设备的索引。在 `sounddevice` 模块中，每个音频设备都由一个唯一的数字索引来标识。这个属性允许用户设置一个默认设备，该设备会在创建音频流时默认使用，除非在创建流时指定了其他设备。

默认情况下，当 `sounddevice` 模块初始化时，它会自动查询 PortAudio 接口并将系统中的默认音频设备的索引赋给 `sounddevice.default.device`。用户可以通过修改这个属性来更改默认的音频设备，这将影响所有后续创建的音频流，除非在创建流时手动指定了设备。

该属性可以设置为单一值，这种情况下对输入和输出设备采用相同的设备索引，也可以设置为一个由两个值组成的元组或列表，分别指定输入和输出设备的设备索引。

下面是一些使用 `sounddevice.default.device` 的示例：

```python
import sounddevice as sd

# 查询所有可用的音频设备
print(sd.query_devices())

# 将默认的音频设备设置为第一个设备
sd.default.device = 0

# 分别设置默认的输入和输出设备
sd.default.device = (1, 2)  # 输入设备索引为1，输出设备索引为2

# 通过设备的名称查询设备索引并设置为默认设备
device_info = sd.query_devices()
input_device_index = next((index for index, d in enumerate(device_info)
                           if 'My Favorite Input' in d['name']), None)
output_device_index = next((index for index, d in enumerate(device_info)
                            if 'My Favorite Output' in d['name']), None)
sd.default.device = (input_device_index, output_device_index)

# 检查当前设置的默认设备索引
print(sd.default.device)  # 输出当前默认的输入和输出设备索引
```

注意：在使用时应确保指定的设备索引或名称是有效的，否则可能会抛出异常。如果所需设备的名称在多个设备中出现，可能需要更具体地指定设备名称或使用设备索引。

最后，`sounddevice.default.device` 的设定将影响所有后续创建的音频流，直到该属性被重新赋值或整个模块被重新加载。系统管理员或用户可以根据需要配置此属性，以确保音频应用程序使用正确的音频设备。

- `sounddevice.default.samplerate`: 默认采样率。

`samplerate`是sounddevice模块中的一个属性，它定义了在创建音频流时使用的默认采样率。采样率是指每秒钟采集或播放的样本数，通常以赫兹（Hz）来度量。更改该属性将影响对`Stream`类实例化对象时未明确指定采样率的所有音频处理函数。

在数字音频处理中，采样率是非常重要的参数，因为它会影响音频的质量和处理的复杂度。标准的CD品质采样率为44100Hz，而专业音频设备的采样率可能是48000Hz或更高。

在sounddevice模块中，如果没有明确给出`samplerate`，那么在使用诸如`play`、`rec`、`playrec`和`Stream`等功能创建音频流时，将使用`sounddevice.default.samplerate`作为默认值，如果它也没有被设置，那么将根据使用的音频设备来确定采样率。

如果想在没有指定具体采样率的情况下使用不同的默认值，可以通过设置`sounddevice.default.samplerate`来实现。

示例用法：

```python
import sounddevice as sd

# 将默认采样率设置为48000Hz
sd.default.samplerate = 48000

# 创建一个新的Stream对象，它将使用上面设置的默认采样率
stream = sd.Stream(...)
```

在上述示例中，我们将默认采样率修改为48000Hz并创建了一个新的Stream对象。由于没有为Stream对象指定采样率，它将使用`samplerate`属性中指定的默认值。

通常，采样率的选择取决于特定应用或音频硬件的要求。例如，在音频播放、录音或音频分析某些特定应用程序中，可能需要选择更高或更低的采样率以实现最佳性能。

如果需要重置`samplerate`为其初始化状态（即PortAudio库的默认设置），可以将其赋值为`None`：

```python
# 重置默认采样率
sd.default.samplerate = None
```

在以上命令执行后，当新的Stream对象被创建且没有明确指定采样率时，将使用PortAudio库提供的默认采样率。

### 2.6. 异常

- `sounddevice.PortAudioError`: 封装PortAudio错误的异常类。

`sounddevice.PortAudioError` 是在使用 `sounddevice` 模块时操作PortAudio库时可能引发的异常类。当PortAudio API调用返回错误时，该异常会被触发。PortAudio是用于音频捕获和播放的跨平台开源音频API，`sounddevice` 模块则为Python环境提供了PortAudio的简洁接口。

在 `sounddevice.PortAudioError` 异常类中包含了通常用于描述错误的多个属性。

**属性**：

- `args`: 包含关于错误的详细信息的元组。元组中的内容可能包含以下元素：

  1. 字符串描述的错误信息。
  2. PortAudio错误代码，即 `PaErrorCode` 的值。
  3. 包含主机API索引号、主机错误代码及主机错误消息的三元组。

**行为**：

- 当在 `sounddevice` 模块中使用PortAudio相关函数时，例如打开或关闭音频流、录音、播放等，如果底层PortAudio库调用失败，可能会抛出 `PortAudioError` 异常。
- 在引发此异常时，它会捕获并提供有关发生错误的具体细节，这有助于调试和识别问题的起因。

以下是如何在代码中处理 `sounddevice.PortAudioError` 的一个例子。

```python
import sounddevice as sd

try:
    # 尝试做一些涉及音频的操作，比如打开音频流
    stream = sd.Stream(samplerate=44100, channels=2)
    stream.start()
    # 更多的音频处理...
except sd.PortAudioError as e:
    # 如果发生PortAudioError异常，我们可以在这里处理它
    print(e)
finally:
    if 'stream' in locals() and stream.active:
        stream.stop()
        stream.close()
```

**注意事项**：

- 通常，当你在正确配置音频I/O设备时无需担心此异常。但在音频设备权限不足、配置不正确或它正被其他应用程序独占使用时，可能会遇到这个错误。
- 正常操作PortAudio时，最好在所有可能出错的地方进行异常捕获，以确保程序的健壮性和异常信息的捕获。
- `sounddevice.PortAudioError` 内部也会包含一个指向具体系统音频层错误的引用（如ASIO、WASAPI、ALSA等），这为进一步的错误诊断提供了便利。

`sounddevice.PortAudioError` 异常是你在使用 `sounddevice` 模块进行音频操作时，与PortAudio交互出现问题的信号。理解和正确处理这个异常对于开发稳定可靠的音频应用程序至关重要。

### 2.7. 回调处理

- `sounddevice.CallbackFlags`: 告知回调函数的音频流状况。

`sounddevice.CallbackFlags` 是一个类，它的实例被用作音频流回调中的状态标志。这些标志表明在处理音频数据时是否发生了输入或输出缓冲区的下溢（underflow）或上溢（overflow）等问题。如果在处理音频数据时遇到此类问题，应该检查音频流的配置，并尝试通过调整采样率、缓冲区大小或其它参数来优化性能。

回调函数的状态参数包含一个 `CallbackFlags` 实例，其中可能包含以下几个布尔属性，用于指示相应的问题是否发生。

- `input_underflow`：
  为 `True` 表示输入缓冲区（recording buffer）中数据量不足，导致需要输入数据时并未能提供足够的数据给音频设备。这可能是因为音频回调函数未能及时处理输入数据，或处理耗时过长导致的。通常，这种情况下听到的会是中断或噪声。

- `input_overflow`：
  为 `True` 表明输入缓冲区中的数据超载，导致一部分数据丢失。在输入流中，这通常是因为数据的到达速度超过了应用程序处理的能力。这可能导致录音中断或失真。

- `output_underflow`：
  为 `True` 表示输出缓冲区（playback buffer）的数据量不够，没有足够的数据写入音频设备用于播放。这会导致播放时听到的音频出现中断或"卡顿"。

- `output_overflow`：
  为 `True` 表明输出缓冲区中的数据溢出，通常这是因为应用程序生成数据的速度慢于音频设备的播放速度。注意，这对于输出流通常并不适用，因为在正常情况下，输出缓冲区不会溢出。

- `priming_output`：
  为 `True` 表明部分或全部输出数据将用于启动（prime）流，输入数据可能为零。当设置`prime_output_buffers_using_stream_callback=True`时，将调用音频流回调来填充初始输出缓冲区，而不使用默认的静音填充。

以下是如何使用 `sounddevice.CallbackFlags` 的一个示例：

```python
import sounddevice as sd

def callback(indata, outdata, frames, time, status):
    if status.input_overflow:
        print('Input overflow!')
    if status.output_underflow:
        print('Output underflow!')
    # 在此进行音频数据处理 ...
    outdata[:] = indata  # 例如，此处将输入直接复制到输出（延迟播放）

stream = sd.Stream(callback=callback)
with stream:
    sd.sleep(10000)  # 使音频流播放一段时间
```

在此示例中，我们定义了一个回调函数 `callback`，它在每次被音频流调用时检查状态，并打印出相应的溢出/下溢问题。利用 `CallbackFlags`，我们可以监视这些情况，并在我们的代码中适当地响应这些错误信息。

注意：过多的缓冲下溢/上溢表明音频处理可能存在性能瓶颈或配置问题，这可能需要代码优化或硬件配置调整。

### 2.8. 配置

#### 2.8.1. `sounddevice.AsioSettings`: 为ASIO设备提供额外的配置

`sounddevice.AsioSettings`类是面向Windows操作系统的音频驱动ASIO (Audio Stream Input/Output) 的专有配置选项。ASIO是由Steinberg公司开发的一种音频驱动规范，它能够在Windows平台上提供低延迟和高性能的音频流。该类提供了使用特定ASIO音频输入/输出设备时的附加设置，通常用于音频应用或数字音频工作站（DAW）中。

使用`AsioSettings`类时，需要提供ASIO通道选择器的参数，这是一个整数列表，指示要使用的ASIO设备的通道编号。

**实例化方法**：

```python
sounddevice.AsioSettings(channel_selectors)
```

**参数**：

- **channel_selectors**：一个整数列表，它指定了要使用的ASIO设备的通道编号（索引从0开始）。这个列表的长度必须和当时使用的输入输出通道数一致。在这个列表中，每个通道编号都应该在设备支持的通道数范围之内。

假设某个ASIO设备有8个通道，而你只想使用其中的第1、4和第5通道，你应该这样设置：

```python
import sounddevice as sd

channels_to_use = [0, 3, 4]  # ASIO通道编号从0开始
asio_settings = sd.AsioSettings(channel_selectors=channels_to_use)
```

这样设置后，当你创建一个音频流时可以用`asio_settings`对象作为`extra_settings`参数。

当你想要对音频捕获和播放的处理有更精细的控制时，特别是在需要低延迟的情况下，ASIO设置就非常有用。例如，对于专业的音频处理和现场表演，使用ASIO驱动和`AsioSettings`可以提供稳定且响应快速的音频输入/输出能力。

**注意事项**：

- ASIO是专为Windows系统设计的，并且需要一块支持ASIO的音频接口硬件。
- ASIO通道选择器是高级设置，只有对声卡和ASIO有较深理解的用户才建议使用。
- 如果不当地使用`channel_selectors`，例如给出了过长或过短的列表、通道编号范围错误，可能会导致程序崩溃或运行异常。

正是由于这些设置提供了对较低层次和较为底层的音频设备参数的控制，因此`AsioSettings`在准确配置和优化音频性能方面扮演着重要角色。

#### 2.8.2. `sounddevice.CoreAudioSettings`: 为Mac OS X Core Audio设备提供额外的配置

`sounddevice.CoreAudioSettings` 是一个用于 Mac OS X Core Audio 设备的特定配置类。它提供了一种方式，让用户可以设置与 Core Audio 相关的高级参数。这个类可以用在 `sounddevice.Stream()` 及其变体中作为 `extra_settings` 参数，或者用于 `sounddevice.default.extra_settings`。

下面详细说明 `sounddevice.CoreAudioSettings` 的构造函数：

```python
class CoreAudioSettings:
    def __init__(self, channel_map=None, change_device_parameters=False,
                 fail_if_conversion_required=False, conversion_quality='max'):
        ...
```

**参数说明**：

- `channel_map` (序列类型，int 类型，可选): 用于只打开 Core Audio 设备特定的通道。`channel_map` 的处理方式在输入和输出通道上是不同的。

  - 对于输入设备，`channel_map` 是指定使用的（从 0 开始计数的）通道号的整数列表。
  - 对于输出设备，`channel_map` 应该与设备的输出通道数长度相同。未使用的通道用 -1 来标记，需要的通道用 0 开始的索引指定。

- `change_device_parameters` (布尔型，可选): 如果设置为 `True`，允许 PortAudio 更改设备的帧大小等参数，这样可以获得更低的延迟，但如果设备正被其他程序使用，就可能会冲突。默认值为 `False`。
- `fail_if_conversion_required` (布尔型，可选): 结合上面的参数使用，若设为 `True`，仅当设备支持精确的采样率时才会打开流。
- `conversion_quality` (字符串，可选): 用于设置 Core Audio 的采样率转换品质，可选项有 `'min'`、`'low'`、`'medium'`、`'high'` 和 `'max'`，默认为 `'max'`。

以下示例演示了如何将 `CoreAudioSettings` 用于音频流的创建和修改 Core Audio 的高级设置：

```python
import sounddevice as sd

# 设置 Core Audio 特定的通道映射和参数
coreaudio_settings = sd.CoreAudioSettings(
    channel_map=[0, 1, -1, -1],  # 用前两个通道，其他的设置为-1
    change_device_parameters=True,
    fail_if_conversion_required=False,
    conversion_quality='high')      # 确保高质量的采样率转换

# 使用特定的 Core Audio 设置创建一个输出流
with sd.OutputStream(device=1, channels=2, extra_settings=coreaudio_settings) as stream:
    # 读取和播放音频数据
    ...

# 也可以将 CoreAudioSettings 对象赋值给默认的 extra_settings
sd.default.extra_settings = coreaudio_settings
```

在这个例子中，`channel_map` 设置了四个通道的映射，指定了仅使用前两个通道，而通过设置 `change_device_parameters` 为 `True`，该设置允许 PortAudio 在需要时更改设备参数以获得更低的延迟。

更多关于 Core Audio 设置的细节可以参考 `PortAudio`_ 和 `Core Audio`_ 的官方文档。

- _PortAudio: http://www.portaudio.com/
- _Core Audio: https://developer.apple.com/documentation/coreaudio

请注意，本示例仅为演示，实际使用中需要根据实际设备及需求适配参数。