# Failure demo of vscode global search
This repository demonstrate a regex global search (Shift+Control+F) failure of the VS Code editor among the files with UNICODE wide characters. 

## Version and environment. 
The version information of the VS Code is following : 
```
Version : 1.95.3 (system setup)
Commit : f1a4fb101478ce6ec82fe9627c43efbf9e98c813
Date: 2024-11-13T14:50:04.152Z
Electron: 32.2.1
ElectronBuildId: 10427718
Chromium: 128.0.6613.186
Node.js: 20.18.0
V8: 12.8.374.38-electron.0
OS: Windows_NT x64 10.0.22631
```
## Description of the failure. 
When there are multiple files which contains UNICODE wide characters, VS code fails to search globally with the following regular expression. 

```
^(?!(<|$|#))
```

This regular expression must match a line which doesn't have following characters at the head of line. 
- <
- $
- \#

But I found VS code cannot match by global search (Shift+Control+F)
## How to reproduce. 
I could reproduce this failure on the following environment. 
- Windows 11. 
- Ubuntu 24.04 on WSL.
- Ubuntu Server 24.04 through SSH connection. 

The procedure is :
1. Download this repository and open as folder from VS Code. 
2. Copy the regex ``^(?!(<|$|#))`` to the global search input. 
3. Enable the regex search. 
4. You will see the file 001.md, 002.md and 003.md is not appeared as found files(See the screen shot below ).
5. These files are appeared as found during you open these file by editor. 
6. In the editor, the above regex match with the appropriate lines (See the screenshot below ). 

## Possible root cause
The PCRE2 may have something wrong. 

According the official Wiki, the VS Code uses ripgrep for the global search engine, if the file is not opened. The ripgrep doesn't support the look-around. So it will be fall back to PCRE2. This is explained in the [Search Issues - Notes on regular expression support](https://github.com/microsoft/vscode/wiki/Search-Issues#notes-on-regular-expression-support) in the vscode repository. 

The opened file will be searched by javascript search engine. So, this problem may not happen. 

## Screenshot 
The 001.md, 002.md and 003.md are not shown as the found file.  
![](image/2024-12-11%20(2).png)
In the editor, the regex match with the appropriate lines. 
![](image/2024-12-11%20(1).png)
