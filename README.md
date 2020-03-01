![Kopy](https://user-images.githubusercontent.com/58651329/75626348-e4b4d380-5c01-11ea-83bf-19e721932f81.png)
The **kopy** package is the Go's common backup files operation, you can copy an entire directory or big files recursively with low CPU memory usage.  This **kopy** package fully implemented in [gokopy](https://github.com/itrepablik/gokopy) backup files software.

# Installation
```
go get github.com/itrepablik/kopy
```

# Usage
To **copy** the entire directory or a folder as an example usage using the **kopy.CopyDir()** method.
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

To **copy any single file**, use the **kopy.CopyFile()** method.
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
	// Example for kopy.CopyFile to copy single any single file only with absolute path.
	// To make directory path separator a universal, in Linux "/" and in Windows "\" to auto change
	// depends on the user's OS using the filepath.FromSlash organic Go's library.
	srcFile := filepath.FromSlash("C://a//aaa.txt")
	dst := filepath.FromSlash("C:\\b")
	msg := `Starts copying the single file:`

	fmt.Println(msg, srcFile)
	Sugar.Infow(msg, "srcFile", srcFile, "dst", dst, "log_time", time.Now().Format(itrlog.LogTimeFormat))

	dest := filepath.FromSlash(filepath.Join(dst, filepath.Base(srcFile)))
	// Starts copying the single file.
	if err := kopy.CopyFile(srcFile, dest, Sugar); err != nil {
		fmt.Println(err)
		Sugar.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		return
	}

	// Give some info back to the user's console and the logs as well.
	msg = `Successfully copied the file:`
	fmt.Println(msg, srcFile)
	Sugar.Infow(msg, "srcFile", srcFile, "dst", dst, "log_time", time.Now().Format(itrlog.LogTimeFormat))
}
```

To copy only the latest modified files based on the cut-off days, use the kopy.CopyMD

# License
Code is distributed under MIT license, feel free to use it in your proprietary projects as well.
