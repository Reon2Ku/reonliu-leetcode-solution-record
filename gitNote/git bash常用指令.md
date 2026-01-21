## 初始化仓库：

点击code，选择SSH地址

~~~
git remote add origin <远程仓库地址>
~~~

此处origin是仓库默认别名。



~~~
git pull origin main --allow-unrelated-histories
~~~

此操作允许合并两个独立开始的仓库历史，如果有readme文件则需使用此步骤



~~~
git push -u origin main
~~~

推送



~~~
git add .
git add <filename>    # 添加特定文件
~~~

git add：将修改添加到暂存区



~~~
git commit -m "my commit msg"
~~~

git commit：将暂存区的内容提交到本地仓库



~~~
git push 
~~~

git push：将本地提交推送到远程仓库
