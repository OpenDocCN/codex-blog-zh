# 如何在 Swift 中组合音频，并以编程方式从 Mac 上的 iTunes/音乐库获取音乐

> 原文：<https://medium.com/codex/how-to-combine-audio-in-swift-and-programmatically-get-music-from-itunes-music-library-on-mac-6c1df23fa679?source=collection_archive---------25----------------------->

与 Swift 中的大多数东西不同，合并音轨是一个相当复杂的过程。

一个很好的起点是阅读 Greg Cerveny 关于这个话题的文章。然而，在这篇短文中，我想说明我是如何做到的，并从我的 Mac 上合并 MP3 播放列表。[这是完整的项目](https://github.com/carlosmbe/Mac-iTunes-Music-Playlist-Exporter)。

![](img/58b5470e19eb5fe1cb03a4c74da11371.png)

照片由[丹尼尔·施鲁迪](https://unsplash.com/@schluditsch?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

## 组合音频

首先，在处理媒体时，我们会想要导入 [AVFoundation](https://developer.apple.com/av-foundation/) 。

```
import AVFoundation
```

其次，基于 Greg 的代码，我们将为**AVMutableCompositionTrack**编写一个扩展，简化附加音轨。

```
extension AVMutableCompositionTrack {
  func append(url: URL) {
    let newAsset = AVURLAsset(url: url)
    let range = CMTimeRangeMake(start: CMTime.zero, duration: newAsset.duration)
    let end = timeRange.end
      if let track = newAsset.tracks(withMediaType: AVMediaType.audio).first {
      try! insertTimeRange(range, of: track, at: end)
       }  else{
print("ERROR - Appending")
       }
    }
}
```

现在完成了。我们需要编写一个函数来获取音频文件并导出它们。假设您已经有了要组合的歌曲的 URL 列表，代码应该如下所示。

```
var arrayOfSongPaths = [URL?]()

func combineSongs(){    
    let composition = AVMutableComposition()
    let compositionAudioTrack = composition.addMutableTrack(withMediaType: AVMediaType.audio, preferredTrackID: kCMPersistentTrackID_Invalid)

    for path in arrayOfSongPaths{
        compositionAudioTrack!.append(url: path!)
    }

    //Built the path like this but you can use any path you want. Just remember to handle permissions.
    let documentDirectoryURL = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first! as NSURL
    let destinationURL = documentDirectoryURL.appendingPathComponent(" resultMerge.m4a")! as URL

    if let assetExport = AVAssetExportSession(asset: composition, presetName: AVAssetExportPresetAppleM4A) {
      assetExport.outputFileType = AVFileType.m4a
        assetExport.outputURL = destinationURL

        assetExport.exportAsynchronously( completionHandler:    {
            //You don't NEED to write these switch cases but they strongly help to debug
            switch assetExport.status {

            case AVAssetExportSession.Status.failed:
                print("failed \(assetExport.error)")
            case AVAssetExportSession.Status.cancelled:
                print("cancelled \(assetExport.error)")
            case AVAssetExportSession.Status.unknown:
                print("unknown\(assetExport.error)")
            case AVAssetExportSession.Status.waiting:
                print("waiting\(assetExport.error)")
            case AVAssetExportSession.Status.exporting:
                print("exporting\(assetExport.error)")

            default:
                print("COMPLETED YAY!!!")
                NSWorkspace.shared.open(documentDirectoryURL as URL)
            }

        })
    }//asset export end

}
```

恭喜你。如果一切都编译和运行，Finder 应该会打开，向您显示一个 m4a，其中包含所有音频文件。

# 从 Mac 音乐库获取本地音乐

![](img/c68e7a0ebdca5ebcf1a118fd383f67bb.png)

[威森王](https://unsplash.com/@wesson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/mac?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

如果您想将 iTunes/Music 中的播放列表编辑成一个音频文件。这部分是给你的。

首先，导入 iTunesLibrary。

```
import iTunesLibrary
```

其次，我们希望获得用户库中的所有播放列表。

```
let library = try! ITLibrary(apiVersion: "1.1")
let playlists = library.allPlaylists
```

假设您知道想要的播放列表的名称。你可以通过这个功能获得所有歌曲的网址。

```
func getPlaylistsData(for funcPlaylistName: String?){

   guard let funcPlaylistName = funcPlaylistName else{
      return
    }
  var stuffIWant: ITLibPlaylist {
    for item in playlists{
      if item.name == funcPlaylistName{
          return item
          }
        }
      return playlists[0] //Just in case in the loop fails
    }

  for song in stuffIWant.items{
    arrayOfSongPaths.append(song.location)
     }
}
```

🎊恭喜你。你完了。干杯🎉

![](img/6ebf1dfd405a097458a734cedc989bc1.png)