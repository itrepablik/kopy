# kopy
The **kopy** package is the Go's common backup files operation, you can copy an entire directory or big files recursively with low CPU memory usage.  This **kopy** package fully implemented in [gokopy](https://github.com/itrepablik/gokopy) backup files software.

# Installation
```
go get github.com/itrepablik/kopy
```

# Usage
To copy the entire directory or a folder as an example usage:
```
package main

import (
	"fmt"
	"path/filepath"
	"time"

	"github.com/itrepablik/itrlog"
	"github.com/itrepablik/kopy"
	"go.uber.org/zap"
)

// Sugar initializes the Zap and Lumberjack package
var Sugar *zap.SugaredLogger

func init() {
	Sugar = itrlog.InitLog(50, 90, "logs", "test_copy_")
}

func main() {
	IgnoreFilesOrFolders := []string{".txt", ".jpg", "folder_name"}

	// Example for the `kopy.CopyDir` which will copy the entire directory or a folder including its
	// sub-folders and contents.
	src, dst, msg := filepath.FromSlash("C:\\a"), filepath.FromSlash("C:\\b"), ""

	msg = `Starts copying the entire directory or a folder: `
	fmt.Println(msg, src)
	Sugar.Infow(msg, "src", src, "log_time", time.Now().Format(itrlog.LogTimeFormat))

	filesCopied, foldersCopied, err := kopy.CopyDir(src, dst, true, IgnoreFilesOrFolders, Sugar)
	if err != nil {
		fmt.Println(err)
		Sugar.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		return
	}

	// Give some info back to the user's console and the logs as well.
	msg = `Successfully copied the entire directory or a folder: `
	fmt.Println(msg, src, ", Number of Folders Copied: ", filesCopied, " Number of Files Copied: ", foldersCopied)
	Sugar.Infow(msg, "src", src, "dst", dst, "folder_copied", filesCopied, "files_copied", foldersCopied, "log_time", time.Now().Format(itrlog.LogTimeFormat))
}
```

# License
Code is distributed under MIT license, feel free to use it in your proprietary projects as well.
