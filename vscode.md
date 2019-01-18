## ターミナルをmsys2にする

setting.json
```
{
    "terminal.integrated.shell.windows": "E:\\Applications\\System\\msys64\\usr\\bin\\bash.exe",
    "terminal.integrated.env.windows": {
        "MSYSTEM": "MINGW64",
        "CHERE_INVOKING": "1",
        "MSYS2_PATH_TYPE": "inherit"
    },
    "terminal.integrated.shellArgs.windows": [
        "--login"
    ],
    "terminal.integrated.cursorStyle": "line"
}
```