![Kopy](https://user-images.githubusercontent.com/58651329/79674357-fa637400-8214-11ea-8e52-dd706f0f85ab.png)
The **kopy** package is the Go's common backup files operation, you can copy an entire directory or big files recursively with low CPU memory usage.  This **kopy** package fully implemented in [gokopy](https://github.com/itrepablik/gokopy) backup files software.

# Installation
```
go get -u github.com/itrepablik/kopy
```

# Usage
To copy the **entire directory or a folder** as an example usage using the **kopy.CopyDir()** method. This will not compress the directory or a folder.
```
package main

import (
	"fmt"
	"path/filepath"
	"time"

	"github.com/itrepablik/itrlog"
	"github.com/itrepablik/kopy"
)

func main() {
	IgnoreFilesOrFolders := []string{".txt", ".jpg", "folder_name"}

	// Example for the `kopy.CopyDir` which will copy the entire directory or a folder including its
	// sub-folders and contents.
	src, dst, msg := filepath.FromSlash("C:/a"), filepath.FromSlash("C:/b"), ""

	msg = `Starts copying the entire directory or a folder: `
	fmt.Println(msg, src)
	itrlog.Infow(msg, "src", src, "log_time", time.Now().Format(itrlog.LogTimeFormat))

	filesCopied, foldersCopied, err := kopy.CopyDir(src, dst, true, IgnoreFilesOrFolders)
	if err != nil {
		fmt.Println(err)
		itrlog.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		return
	}

	// Give some info back to the user's console and the logs as well.
	msg = `Successfully copied the entire directory or a folder: `
	fmt.Println(msg, src, ", Number of Folders Copied: ", filesCopied, " Number of Files Copied: ", foldersCopied)
	itrlog.Infow(msg, "src", src, "dst", dst, "folder_copied", filesCopied, "files_copied", foldersCopied, "log_time", time.Now().Format(itrlog.LogTimeFormat))
}
```

To **copy any single file**, use the **kopy.CopyFile()** method.  This will not compress the file.
```
package main

import (
	"fmt"
	"path/filepath"
	"time"

	"github.com/itrepablik/itrlog"
	"github.com/itrepablik/kopy"
)

func main() {
	// Example for kopy.CopyFile to copy single any single file only with absolute path.
	// To make directory path separator a universal, in Linux "/" and in Windows "\" to auto change
	// depends on the user's OS using the filepath.FromSlash organic Go's library.
	srcFile := filepath.FromSlash("C:/a/aaa.txt")
	dst := filepath.FromSlash("C:/b")
	msg := `Starts copying the single file:`

	fmt.Println(msg, srcFile)
	itrlog.Infow(msg, "srcFile", srcFile, "dst", dst, "log_time", time.Now().Format(itrlog.LogTimeFormat))

	dest := filepath.FromSlash(filepath.Join(args[1], filepath.Base(src)))
	
	// Starts copying the single file.
	if err := kopy.CopyFile(srcFile, dest, dst); err != nil {
		fmt.Println(err)
		itrlog.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		return
	}

	// Give some info back to the user's console and the logs as well.
	msg = `Successfully copied the file:`
	fmt.Println(msg, srcFile)
	itrlog.Infow(msg, "srcFile", srcFile, "dst", dst, "log_time", time.Now().Format(itrlog.LogTimeFormat))
}
```

To copy only the **latest modified files** based on the cut-off days, use the **kopy.WalkDIRModLatest()** method.  This will not compress the copied files.
```
package main

import (
	"fmt"
	"path/filepath"
	"time"

	"github.com/itrepablik/itrlog"
	"github.com/itrepablik/kopy"
)

func main() {
	NumFilesCopied := 0

	// Get the list of ignored file types or entire folder name.
	IgnoreFileTypesOrFolder := []string{".txt", ".jpg", "folder_name"}

	// To make directory path separator a universal, in Linux "/" and in Windows "\" to auto change
	// depends on the user's OS using the filepath.FromSlash organic Go's library.
	src := filepath.FromSlash("C:/a")
	dst := filepath.FromSlash("C:/b")

	msg := `Starts copying the latest files from:`
	fmt.Println(msg, src)
	itrlog.Infow(msg, "src", src, "dst", dst, "log_time", time.Now().Format(itrlog.LogTimeFormat))

	// Starts copying the latest files from.
	if err := kopy.WalkDIRModLatest(src, dst, -7, true, IgnoreFileTypesOrFolder); err != nil {
		fmt.Println(err)
		itrlog.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		return
	}

	// Give some info back to the user's console and the logs as well.
	msg = `Successfully copied the latest files from:`
	fmt.Println(msg, src, " Number of Files Copied: ", NumFilesCopied)
	itrlog.Infow(msg, "src", src, "dst", dst, "copied_files", NumFilesCopied, "log_time", time.Now().Format(itrlog.LogTimeFormat))
}
```

To **compress the entire directory or a folder**, use the **kopy.CompressDIR()** method.  It compresses the entire directory or a folder using the **.tar.gz** compression.
```
package main

import (
	"bytes"
	"fmt"
	"io"
	"os"
	"path"
	"path/filepath"
	"time"

	"github.com/itrepablik/itrlog"
	"github.com/itrepablik/kopy"
)

func main() {
	// To make directory path separator a universal, in Linux "/" and in Windows "\" to auto change
	// depends on the user's OS using the filepath.FromSlash organic Go's library.
	src := filepath.FromSlash("C:/a")
	dst := filepath.FromSlash("C:/b")
	IgnoreFilesOrFolders := []string{".txt", ".jpg", "folder_name"}

	msg := `Start compressing the directory or a folder:`
	fmt.Println(msg, src)
	itrlog.Infow(msg, "src", src, "dst", dst, "log_time", time.Now().Format(itrlog.LogTimeFormat))

	// Compose the zip filename
	fnWOext := kopy.FileNameWOExt(filepath.Base(src)) // Returns a filename without an extension.
	zipDir := fnWOext + kopy.ComFileFormat

	// To make directory path separator a universal, in Linux "/" and in Windows "\" to auto change
	// depends on the user's OS using the filepath.FromSlash organic Go's library.
	zipDest := filepath.FromSlash(path.Join(dst, zipDir))

	// Start compressing the entire directory or a folder using the tar + gzip
	var buf bytes.Buffer
	if err := kopy.CompressDIR(src, &buf, IgnoreFilesOrFolders); err != nil {
		fmt.Println(err)
		itrlog.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		return
	}

	// write the .tar.gzip
	os.MkdirAll(dst, os.ModePerm) // Create the root folder first
	fileToWrite, err := os.OpenFile(zipDest, os.O_CREATE|os.O_RDWR, os.FileMode(600))
	if err != nil {
		itrlog.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		panic(err)
	}
	if _, err := io.Copy(fileToWrite, &buf); err != nil {
		itrlog.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		panic(err)
	}
	defer fileToWrite.Close()

	msg = `Done compressing the directory or a folder:`
	fmt.Println(msg, src)
	itrlog.Infow(msg, "dst", zipDest, "log_time", time.Now().Format(itrlog.LogTimeFormat))
}
```

To **decompress** the **.tar.gz** file, use the **kopy.ExtractTarGz()** method.
```
package main

import (
	"fmt"
	"os"
	"path/filepath"
	"time"

	"github.com/itrepablik/itrlog"
	"github.com/itrepablik/kopy"
)

func main() {
	// To make directory path separator a universal, in Linux "/" and in Windows "\" to auto change
	// depends on the user's OS using the filepath.FromSlash organic Go's library.
	src := filepath.FromSlash("C:/b/a.tar.gz")

	msg := `Start decompressing the folder or a directory:`
	fmt.Println(msg, src)
	itrlog.Infow(msg, "src", src, "log_time", time.Now().Format(itrlog.LogTimeFormat))

	r, err := os.Open(src)
	if err != nil {
		itrlog.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		fmt.Println("error")
		return
	}
	if err := kopy.ExtractTarGz(r, src, true); err != nil {
		fmt.Println(err)
		itrlog.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		return
	}

	msg = `Done decompressing the folder or a directory:`
	fmt.Println(msg, src)
	itrlog.Infow(msg, "src", src, "log_time", time.Now().Format(itrlog.LogTimeFormat))
}
```

To **compress any single file**, use the **kopy.ComFiles** method.  It uses the **zip** file compression.
```
package main

import (
	"fmt"
	"os"
	"path"
	"path/filepath"
	"time"

	"github.com/itrepablik/itrlog"
	"github.com/itrepablik/kopy"
)

func main() {
	// Use this function to auto detect file path structure.
	src := filepath.FromSlash("C:/a/file.txt")
	dst := filepath.FromSlash("C:/b")

	// Start the process.
	msg := `Start compressing the file:`
	fmt.Println(msg, src)
	itrlog.Errorw(msg, "src", src, "dst", dst, "log_time", time.Now().Format(itrlog.LogTimeFormat))

	// Compose the zip filename
	fnWOext := kopy.FileNameWOExt(filepath.Base(src)) // Returns a filename without an extension.
	zipFileName := fnWOext + ".zip"

	// To make directory path separator a universal, in Linux "/" and in Windows "\" to auto change
	// depends on the user's OS using the filepath.FromSlash organic Go's library.
	zipDest := filepath.FromSlash(path.Join(dst, zipFileName))

	// List of Files to compressed.
	files := []string{src}

	os.MkdirAll(dst, os.ModePerm) // Create the root folder first
	if err := kopy.ComFiles(zipDest, files); err != nil {
		fmt.Println(err)
		itrlog.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		return
	}

	msg = `Done compressing the file:`
	fmt.Println(msg, src)
	itrlog.Infow(msg, "src", src, "dst", zipDest, "log_time", time.Now().Format(itrlog.LogTimeFormat))
}
```

To **decompress** any single **zip** file, use the **kopy.Unzip** method.
```
package main

import (
	"fmt"
	"path/filepath"
	"time"

	"github.com/itrepablik/itrlog"
	"github.com/itrepablik/kopy"
)

func main() {
	// To make directory path separator a universal, in Linux "/" and in Windows "\" to auto change
	// depends on the user's OS using the filepath.FromSlash organic Go's library.
	src := filepath.FromSlash("C:/b/file.zip")

	msg := `Start decompressing the file:`
	fmt.Println(msg, src)
	itrlog.Errorw(msg, "src", src, "log_time", time.Now().Format(itrlog.LogTimeFormat))

	if err := kopy.Unzip(src, true); err != nil {
		fmt.Println(err)
		itrlog.Errorw("error", "err", err, "log_time", time.Now().Format(itrlog.LogTimeFormat))
		return
	}

	msg = `Done decompressing the file:`
	fmt.Println(msg, src)
	itrlog.Errorw(msg, "src", src, "log_time", time.Now().Format(itrlog.LogTimeFormat))
}
```

# Subscribe to Maharlikans Code Youtube Channel:
Please consider subscribing to my Youtube Channel to recognize my work on this package. Thank you for your support!
https://www.youtube.com/channel/UCdAVUmldU9Jn2VntuQChHqQ/

# License
Code is distributed under MIT license, feel free to use it in your proprietary projects as well.
