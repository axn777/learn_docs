## 环境变量

```bat
# prompt
echo %PATH%
# power shell
$env:PATH -split ';'

			# 添加到 conda 路径，！！！！！！！不要做这个修改
			conda env config vars set PATH="D:\cmdTools;%PATH%"
			
conda 需要加入到 env/envname/Scripts 目录下生效
			




```

