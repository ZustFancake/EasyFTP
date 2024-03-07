**NOTE: THIS PACKAGE IS IN TEST VERSION YET.**

# EasyFTP

![enter image description here](https://github.com/ZustFancake/EasyFTP/blob/main/EasyFTP-logo-highscaled.png?raw=true)

EasyFTP is a Python library that simplifies the process of interacting with FTP (File Transfer Protocol) servers. With EasyFTP, you can easily upload, download, delete, and list files and directories on remote FTP servers using a simple and intuitive interface.

## Features

- Connect to FTP servers with ease.
- Upload files and directories to remote servers.
- Download files and directories from remote servers.
- Delete files and directories on remote servers.
- List files and directories on remote servers.
- *(not implemented)* ~~Support for both FTP and secure FTP (FTPS).~~

## Installation

You can install EasyFTP via pip:

```bash
pip install EasyFTP
```

## Usage
<details>
<summary> Structure of the directory /example used here </summary>
	
```
/examples
├── dir1
│   ├── a.png
│   ├── b.png
│   └── c.jpg
├── dir2
│   └── subdir-a
│       ├── random.key
│       └── randon.key
├── dir3
│   ├── some.binary
│   └── some.binary.X
├── example.py
└── example.py2
```

</details>

### Establishing connection
* **EasyFTP.connect(self, host: str, port: int, username: str, password: str, timeout: int = None) -> int**
```py
from EasyFTP import EasyFTP 

with EasyFTP() as session:
	session.connect("192.168.1.1", 21, "admin", "12345678", timeout = 30)
	# Change it to your option, and timeout is not necessary.
```
### Listing files on remote directory
* **EasyFTP.ls(self, path: Optional[str] = None) -> list**
```py
from EasyFTP import EasyFTP 

with EasyFTP() as session:
	session.connect("192.168.1.1", 21, "admin", "12345678", timeout = 30)
	# Change it to your option, and timeout is not necessary.
	l = session.ls("/examples")
	# Directory argument is not necessary;
	# It will print structure of current directory when there is no argument.
	print(l)
```
It will print, for example: `["dir1", "dir2", "dir3", "example.py", "example.py2"]`

### Changing directory in remote (and printing current working directory)
* **EasyFTP.cd(self, path: Optional[str] = None) -> str**
* **EasyFTP.pwd(self) -> str**
```py
from EasyFTP import EasyFTP

with EasyFTP() as session:
	session.connect("192.168.1.1", 21, "admin", "12345678", timeout = 30)
	# Change it to your option, and timeout is not necessary.
	
	session.cd("/examples")
	# This will change your directory to /examples.
	# NOTE: Relative path is not tested yet.

	print(session.cd())
	# This will print "/examples", which is your current working directory.
	# It does this when there is no argument passed to it.

	print(session.pwd())
	# It works like session.cd().
```
### Downloading specific file(s) from remote
* **EasyFTP.download(self, remote_path: str, local_path: str, filt : Optional[[Filter.Filter](https://github.com/ZustFancake/EasyFTP/blob/main/docs/Filter.md)] = None) -> None**
```py
from EasyFTP import EasyFTP 

with EasyFTP() as session:
	session.connect("192.168.1.1", 21, "admin", "12345678", timeout = 30)
	# Change it to your option, and timeout is not necessary.

	# Low-level example.
	# This works by reading file on remote and retrieve it as string,
	# and then write it to your desired file manually.
	# This will raise FTPError if you don't have permission to
	# read files, write permission to your local file,
	# or error(s) not described here. It can be anything.
	content = session.read("/examples/example.py")
	with open("./example.py", encoding = "utf-8") as f:
		f.write(content)

	# This will change your directory to /examples.
	# So you won't need to specify path to file IF IT EXISTS IN THE PATH.
	# NOTE: Relative path is not tested yet.
	session.cd("/examples")

	# High-level example.
	# This works by reading file on remote and retrieve it as file,
	# which will be saved to specified file(s)/path which local_path variable indicates.
	# This can raise error, too. Read them carefully if it happened.
	# TODO : Return whether or not it has succeeded.

	# Downloading one file.
	session.download("example.py", "example.py")

	# TODO : Downloading multiple files using wildcard(s).
	# session.download("ex*.py*", ".")

	# Downloading a directory.
	# local_file MUST indicate a directory to do this.
	# The subdirectory(subdirectories) in the directory will be downloaded too.
	# TODO : Add filter object to filter files to be downloaded.
	session.download("dir2", ".")
```

### Uploading file to remote
* **EasyFTP.upload(self, local_path: str, remote_path: str, filt : Optional[[Filter.Filter](https://github.com/ZustFancake/EasyFTP/blob/main/docs/Filter.md)] = None) -> None:**
* **EasyFTP.upload(self, local_path: str, remote_path: str, filt : Optional[] = None) -> None:**
```py
from EasyFTP import EasyFTP 

with EasyFTP() as session:
	session.connect("192.168.1.1", 21, "admin", "12345678", timeout = 30)
	# Change it to your option, and timeout is not necessary.

	# Low-level example.
	# This works by converting encoded string it as BytesIO,
	# and then write it to your desired remote file manually.
	# This will raise FTPError if you don't have permission to
	# read files, write permission to your local/remote file,
	# or error(s) not described here. It can be anything.
	session.write("/example/example.py3", "print('hello world!')")

	# This will change your directory to /examples.
	# So you won't need to specify path to file IF IT EXISTS IN THE PATH.
	# NOTE: Relative path is not tested yet.
	session.cd("/examples")

	# High-level example.
	# This works by reading file on local and retrieve it as binary,
	# which will be saved to specified file(s)/path which remote_path variable indicates.
	# This can raise error, too. Read them carefully if it happened.
	# TODO : Return whether or not it has succeeded.

	# Uploading one file.
	session.upload("example.py4", "example.py4")

	# TODO : Uploading multiple files using wildcard(s).
	# session.download("ex*.py*", ".")

	# Uploading a directory.
	# remote_file MUST indicate a directory to do this.
	# The subdirectory(subdirectories) in the directory will be uploaded too.
	# TODO : Add filter object to filter files to be uploaded.
	session.download("./new_dir", ".")
```

### Deleting file from remote
* **EasyFTP.delete(self, filename: str, filt : Optional[[Filter.Filter](https://github.com/ZustFancake/EasyFTP/blob/main/docs/Filter.md)] = None, path : Optional[str] = None) -> None**
```python
from EasyFTP import EasyFTP 

with EasyFTP() as session:
	session.connect("192.168.1.1", 21, "admin", "12345678", timeout = 30)
	# Change it to your option, and timeout is not necessary.

	# This will remove example.py from current directory,
	# But this will raise error because there is no file named
	# example.py in the root directory, it is in /example.
	# So be careful, there are always mistakes and errors.
	session.delete("example.py")

	# If you want to delete multiple files at once, please refer to EasyFTP.Filter module guide.
```

### Renaming a file
* **EasyFTP.rename(self, from_name: str, to_name: str) -> None**
* **EasyfTP.mv(self, origin: str, dest: str) -> None**
```py
from EasyFTP import EasyFTP 

with EasyFTP() as session:
	session.connect("192.168.1.1", 21, "admin", "12345678", timeout = 30)
	# Change it to your option, and timeout is not necessary.

	# This will change your directory to /examples.
	# So you won't need to specify path to file IF IT EXISTS IN THE PATH.
	# NOTE: Relative path is not tested yet.
	session.cd("/examples")

	# This will rename "example.py2" to "example.py".
	# If you are not sure about using this function or
	# you are used to linux systems, you can use EasyFTP.mv().
	# Both functions can move/rename directories, too.
	session.rename("example.py2", "example.py") # session.mv("example.py", "example.py2")
	
```


### Making directory
* **EasyFTP.mkdir(self, path: str) -> None**
```py
from EasyFTP import EasyFTP 

with EasyFTP() as session:
	session.connect("192.168.1.1", 21, "admin", "12345678", timeout = 30)
	# Change it to your option, and timeout is not necessary.
	
	# This will create directory on /examples .
	session.mkdir("/examples/new_dir2")

	# This will change your directory to /examples.
	# NOTE: Relative path is not tested yet.
	session.cd("/examples")

	# This will create directory on your current directory.
	session.mkdir("/examples/new_dir3")
```


### Changing File Permissions on Remote FTP Server
* **EasyFTP.chmod(self, remote_path: str, perm: int, mode: Literal["oct", "bin", "int"] = "int") -> None**
> This method uses "SITE CHMOD" command on remote to change the permission of file. Your machine must support this command.
```python
from EasyFTP import EasyFTP 

with EasyFTP() as session:
    session.connect("192.168.1.1", 21, "admin", "12345678", timeout=30)
    # Change it to your option, and timeout is not necessary.

    # Change the permission of "example.txt" to 644 using integer mode
    session.chmod("example.txt", 644)

    # Change the permission of "example.txt" to 755 using octal mode
    session.chmod("example.txt", 0o755, mode="oct")

    # Change the permission of "example.txt" to 777 using binary mode
    session.chmod("example.txt", 0b111111111, mode="bin")
```

## Using `EasyFTP.Filter`
Refer to its [documentation](https://github.com/ZustFancake/EasyFTP/blob/main/docs/Filter.md) for usage instructions and examples.
