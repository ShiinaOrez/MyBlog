## 所以为什么README我要放一些好用的tips:

[直接修改表中全部字段的charset](#直接修改表中全部字段的charset.)

[使用脚本批量修改 git commit history 中的 User 和 Email](#使用脚本批量修改 git commit history 中的 User 和 Email)

#### 直接修改表中全部字段的charset.

```sql
alter table `tablename` convert to charset utf8;  
```

#### 使用go test时，如果不设置GOMUDULE111环境变量，将不使用mod

#### 使用脚本批量修改 git commit history 中的 User 和 Email

 ```shell
 #!/bin/sh

git filter-branch --env-filter '
OLD_EMAIL="your-old-email@example.com"
CORRECT_NAME="Your Correct Name"
CORRECT_EMAIL="your-correct-email@example.com"
if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$CORRECT_NAME"
    export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$CORRECT_NAME"
    export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
 ```
