# how to upload a file to github

teminal ctrl+shift+~ in vscode

```
git init
git add README.md
git commit -m 'update'
git branch -M master
git remote rm origin    (如果远程有)
git remote add origin https://github.com/sak1/uxsinodb.git
git remote add origin [sak7@qq.com]:tyler1974/sak1/uxsinodb.git
git push -u origin master

git remote add origin https://github.com/sak1/python.git

if push error
git push -f origin master
```

——）
ERROR
10054

```
git config --global http.sslVerify "false"
git config --global http.sslVerify false
ERROR
443
git config --global --unset http.proxy
git config --global --unset https.proxy

git remote add origin [sak7@qq.com]:tyler1974/sak1.git

If you use SSH authentication, check that your key is added to the ssh-agent and associated with your account.

uxsinodb document for programer & DBA
git remote add origin https://github.com/sak1/python.git

git config --global user.name "sak1"
git config --global user.email "31940948+sak1@users.noreply.github.com"
```

