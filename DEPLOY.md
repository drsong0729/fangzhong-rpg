# 部署到 GitHub Pages

在你自己的「終端機」App（不是這裡）貼上下面的指令。

## 第一次部署

如果你電腦有裝 gh（GitHub CLI）：

```bash
cd ~/Documents/fangzhong-rpg
gh repo create drsong0729/fangzhong-rpg --public --source=. --remote=origin --push
gh api -X POST repos/drsong0729/fangzhong-rpg/pages -f "source[branch]=main" -f "source[path]=/"
```

沒裝 gh 的話，先到 https://github.com/new 手動開一個 **public** 的 `fangzhong-rpg`
（不要勾 Add a README），然後：

```bash
cd ~/Documents/fangzhong-rpg
git remote add origin https://github.com/drsong0729/fangzhong-rpg.git
git push -u origin main
```

再到 repo 的 **Settings → Pages**，Source 選 `Deploy from a branch`，
Branch 選 `main` / `(root)`，按 Save。

網址：**https://drsong0729.github.io/fangzhong-rpg/**
（第一次大約等 1～2 分鐘才會生效。）

## 之後更新

換上新的 index.html 之後：

```bash
cd ~/Documents/fangzhong-rpg
git add -A && git commit -m "更新" && git push
```
