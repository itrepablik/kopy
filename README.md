# kopy
The **kopy** package is the Go's common backup files operation, you can copy an entire directory or big files recursively with low CPU memory usage.  This **kopy** package fully implemented in [gokopy](https://github.com/itrepablik/gokopy) backup files software.

# Installation
```
go get github.com/itrepablik/kopy
```

# Usage
```
// Starts copying the entire directory or a folder.
  filesCopied, foldersCopied, err = kopy.CopyDir(src, dest, IsLogCopiedFile, IgnoreFT, Logger, Sugar)
  if err != nil {
    fmt.Println(err)
    Sugar.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
    return
  }
```

# License
Code is distributed under MIT license, feel free to use it in your proprietary projects as well.
