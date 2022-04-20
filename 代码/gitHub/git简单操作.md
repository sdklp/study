一、切换到本地仓库目录，配置用户名和邮箱
git config --global user.name 'sdklp'    //配置用户名：GitHub上的用户名
git config --global user.email '617100658@qq.com'    //配置邮箱：GitHub上注册时用的邮箱
git config -l   //显示配置信息

二、创建git本地仓库
git init   //命令用来创建一个新的git仓库
git remote add origin https://github.com/sdklp/deviceManageSoftwareWithMongoDB.git

git status

git add *

git commit -m 'testcommit'

git push -u origin master