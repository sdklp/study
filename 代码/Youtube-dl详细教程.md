## Youtube-dl详细教程与初学上手示例

### 1. 下载视频或播放列表

要从 Youtube 下载视频或整个视频播放列表，只需直接使用 URL 即可：

```
youtube-dl https://www.youtube.com/watch?v=iJvr0VPsn-s
```

![img](https://img.sysgeek.cn/img/2019/08/youtube-dl-examples-2.jpg)

如果要指定视频下载之后的名称，可以使用如下方式：

```
youtube-dl -o 'A REAL Back to School Laptop Guide.mp4' https://www.youtube.com/watch?v=iJvr0VPsn-s
```

![img](https://img.sysgeek.cn/img/2019/08/youtube-dl-examples-3.jpg)

当然，你还可以在下载视频时附加更多详细信息，可用的参数就有：标题、上传者名称（频道名称）和视频上传日期等：

```
youtube-dl -o '%(title)s by %(uploader)s on %(upload_date)s in %(playlist)s.%(ext)s' https://www.youtube.com/watch?v=iJvr0VPsn-s
```

### 2. 下载多个视频

有时，我们需要一次从 Youtube 上下载多个不同的视频，此时我们只需用 空格 将多个 URL 分隔开即可：

```
youtube-dl <url1> <url2>
```

或者，您可以将要下载视频的 URL 全部放在文本文件中，并将其作为参数传递给 Youtube-dl 也行：

```
youtube-dl -a url.txt
```

以上命令将下载 url.txt 文件中所有 URL 指向的视频。

### 3. 只下载（视频中的）音频

Youtube-dl 允许我们仅从 Youtube 视频下载其音频，例如：

```
youtube-dl -x https://www.youtube.com/watch?v=iJvr0VPsn-s
```

![img](https://img.sysgeek.cn/img/2019/08/youtube-dl-examples-4.jpg)

默认情况下，Youtube-dl 将以 Ogg（opus）格式保存音频，如果想以任何其他格式下载音频，例如 mp3 请运行：

```
youtube-dl -x --audio-format mp3 https://www.youtube.com/watch?v=iJvr0VPsn-s
```

![img](https://img.sysgeek.cn/img/2019/08/youtube-dl-examples-5.jpg)

此命令将从给定的视频/播放列表下载音频，将其转换为 MP3 并将其保存在当前目录中。

> 注意：您应该安装 ffmpeg 或 avconv 将文件转换为 mp3 格式。

### 4. 下载带有描述、元数据、注释、字幕和缩略图的视频

要下载视频及其他详细信息，如：说明、元数据、注释、字幕和缩略图等，请使用以下命令：

```
youtube-dl --write-description --write-info-json --write-annotations --write-sub --write-thumbnail https://www.youtube.com/watch?v=iJvr0VPsn-s
```

![img](https://img.sysgeek.cn/img/2019/08/youtube-dl-examples-6.jpg)

### 5. 列出所有可用的音/视频格式

Youtube 网站上的视频和音频会被自动转码成多种音/视频格式，要查看某个视频或播放列表所有可下载的音/视频格式，请使用以下命令：

```
youtube-dl --list-formats https://www.youtube.com/watch?v=iJvr0VPsn-s
```

或者笔者常用的简写方式：

```
youtube-dl -F https://www.youtube.com/watch?v=iJvr0VPsn-s
```

![img](https://img.sysgeek.cn/img/2019/08/youtube-dl-examples-7.jpg)

如上图所示，Youtube-dl 列出了给定视频的所有可用格式，从左到右分别为：**format code**（视频格式代码）、**extension**（扩展名）、**resolution**（分辨率）和 **note**（注释）。当您想要以特定质量或格式下载视频时，先查看一下有哪些可用，会非常便利。

### 6. 以某种质量和/或格式下载视频

默认情况下，Youtube-dl 将自主选择最佳质量的视频下载。 但是，也可以以特定的质量或格式来下载视频或播放列表。

Youtube-dl 支持以下品质：

- best 选择最佳质量的音/视频文件
- worst 选择质量最差的格式（视频和音频）
- bestvideo 选择最佳质量的仅视频格式（例如DASH视频），可能无法使用。
- worstvideo 选择质量最差的纯视频格式，可能无法使用。
- bestaudio 选择最优质的音频格式，可能无法使用。
- worstaudio 选择质量最差的音频格式，可能无法使用。

例如，如果要自动选择并下载最佳质量格式（音频和视频），只需使用以下命令：

```
youtube-dl -f best https://www.youtube.com/watch?v=iJvr0VPsn-s
```

![img](https://img.sysgeek.cn/img/2019/08/youtube-dl-examples-8.jpg)

同样，要以最佳质量仅下载音频，可执行：

```
youtube-dl -f bestaudio https://www.youtube.com/watch?v=iJvr0VPsn-s
```

![img](https://img.sysgeek.cn/img/2019/08/youtube-dl-examples-9.jpg)

您还可以组合使用以下不同的格式选项：

```
youtube-dl -f bestvideo+bestaudio https://www.youtube.com/watch?v=iJvr0VPsn-s
```

![img](https://img.sysgeek.cn/img/2019/08/youtube-dl-examples-10.jpg)

上述命令将**分别下载***最高质量的仅视频*和*最高质量的纯音频格式*，再用 ffmpeg 或 avconv 合并成一个最佳质量的 mkv 文件；如果您不想合并，请将 + （加号）替换为 , （逗号）即可分别得到最高质量的音频和视频（两个文件），如下所示：

```
youtube-dl -f 'bestvideo,bestaudio' https://www.youtube.com/watch?v=iJvr0VPsn-s
```

### 7. 通过视频代码下载文件（常用方法）

前面**方法 5** 已经提到过，所有 Youtube 视频都有格式代码，我们可以用它来下载特定质量的视频。

例如，先用**方法 5** 查看所有可用的音/视频格式及其对应的**format code**（视频格式代码）：

```
youtube-dl -F https://www.youtube.com/watch?v=iJvr0VPsn-s
```

![img](https://img.sysgeek.cn/img/2019/08/youtube-dl-examples-7.jpg)

再通过代码来下载指定的音/视频格式，例如要下载 best 品质（格式代码为 22）的视频文件，则执行以下命令：

```
youtube-dl -f 22 https://www.youtube.com/watch?v=iJvr0VPsn-s
```

![img](https://img.sysgeek.cn/img/2019/08/youtube-dl-examples-11.jpg)

从播放列表下载视频时，某些视频可能没有相同的格式。 在这种情况下，可以按首选顺序指定多个格式代码，例如：

```
youtube-dl -f 22/17/18 <playlist_url>
```

根据上面的示例，Youtube-dl 将以格式 22 下载视频（如果可用）；如果格式 22不可用，则它将下载格式 17（如果可用）；如果格式 22 和 17 都不可用，最后尝试下载格式 18。如果所有格式代码都不匹配，Youtube-dl 会报出提示。还需要注意的是，斜杠是左关联的，即最左侧的格式代码是首选。

### 8. 通过文件扩展名下载音/视频

以您的首选格式下载视频，例如 MP4，只需执行：

```
youtube-dl --format mp4 https://www.youtube.com/watch?v=iJvr0VPsn-s
```

或者

```
youtube-dl -f mp4 https://www.youtube.com/watch?v=iJvr0VPsn-s
```

如我在上一节中已经提到的那样，某些视频可能无法以您的首选格式提供。 在这种情况下，Youtube-dl 将下载其他最佳可用格式。 例如，此命令将下载最佳质量的 MP4 格式文件。 如果 MP4 格式不可用，则它将下载其他最佳可用格式。

```
youtube-dl -f 'bestvideo[ext=mp4]+bestaudio[ext=m4a]/best[ext=mp4]/best' https://www.youtube.com/watch?v=iJvr0VPsn-s
```

### 9. 限制下载视频的大小

从 Youtube 播放列表下载多个视频时，您可能只想下载特定大小的视频。例如，此命令不会下载任何小于指定大小的视频，例如 100MB：

```
youtube-dl --min-filesize 100M <playlist_url>
```

如果您不想下载大于给定大小的视频，可以这样：

```
youtube-dl --max-filesize 100M <playlist_url>
```

我们还可以用组合格式，选择运算符来下载特定大小的视频。例如，以下命令将下载最佳视频格式但不大于 100MB 的视频：

```
youtube-dl -f 'best[filesize<100M]' https://www.youtube.com/watch?v=iJvr0VPsn-s
```

### 10. 按日期下载视频

Youtube-dl 允许我们按照上传日期来筛选和下载视频或播放列表，例如要下载 2019 年 8 月 1 日上传的视频，可以使用：

```
youtube-dl --date 20190801 <URL>
```

下载在特定日期或之前上传的视频：

```
youtube-dl --datebefore 20190801 <URL>
```

下载在特定日期或之后上传的视频：

```
youtube-dl --dateafter 20190101 <URL>
```

仅下载过去 6 个月内上传的视频：

```
youtube-dl --dateafter now-6months <URL>
```

下载特定时间段内（例如 2018 年 1 月 1 日至 2019 年 1 月 1 日）上传的视频：

```
youtube-dl --dateafter 20180101 --datebefore 20190101 <URL>
```

### 11. 从播放列表下载特定的视频

从播放列表下载特定的视频，是 Youtube-dl 的另一个非常有用的功能。例如，要从播放列表下载第 10 个文件，可使用：

```
youtube-dl --playlist-items 10 <playlist_url>
```

同样，要下载多个指定的文件，只需用逗号分隔：

```
youtube-dl --playlist-items 2,3,7,10 <playlist_url>
```

当然，也可以按序号来指定要下载范围，例如从第 10 个开始，直接下载完整个列表：

```
youtube-dl --playlist-start 10 <playlist_url>
```

或者在播放列表中仅下载从第 2 到第 5 的文件：

```
youtube-dl --playlist-start 2 --playlist-end 5 <playlist_url>
```

### 12. 仅下载适用于特定年龄的视频

Youtube-dl 的另一个特点是，允许我们只下载适合指定适用年龄的视频。例如，要从播放列表中下载所有未标记为「[NSFW](https://zh.wikipedia.org/wiki/NSFW)」或年龄限制为 7 岁儿童的「Let’s Play」视频，可使用：

```
youtube-dl --match-title "let's play" --age-limit 7 --reject-title "nsfw" <playlist_url>
```

### 13. 使用帮助

通过上述示例的介绍，相信已经能够满足绝大多数用户对 Youtube 视频下载和 youtube-dl 的使用需求了。有关更多详细信息，请参阅 Youtube-dl 帮助：

```
youtube-dl --help
```