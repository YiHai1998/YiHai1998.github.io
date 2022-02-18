# 编译

Mac编译教程：https://www.cnblogs.com/laolieren/p/build_grafana_on_mac.html

## 后端

```
go run build.go setup
go run build.go build 
```



```
bin/darwin-arm64/grafana-server -homepath /Users/glong/project/grafana
```

## 前端

```
# 如果没有安装yarn
sudo npm install -g yarn

yarn install

yarn start # yarn start hot 热部署，可以修改，汉化
```

```
[./public/app/plugins/panel/nodeGraph/layout.worker.js] 135 bytes {default~app} [not cacheable] [built]
[./public/sass/grafana.dark.scss] 39 bytes {dark} [built]
[./public/sass/grafana.light.scss] 39 bytes {light} [built]
    + 4915 hidden modules
No issues found.
```

说明成功

http://localhost:3000/



## 上传服务器

```
go run build.go setup
go run build.go build 
```



```
bin/linux-amd64/grafana-server -homepath /opt/module/grafana
```



## 汉化

教程：https://www.bilibili.com/read/cv6735751/

修改服务器上的/usr/share/grafana/public/app

```
修改登录页

app/core/components/Login/LoginForm.tsx

修改Change Password

app/features/profile/ChangePasswordPage.tsx 

app/features/profile/ChangePasswordForm.tsx 


修改偏好设置Preferences

app/features/org/SelectOrgCtrl.ts

app/core/components/SharedPreferences/SharedPreferences.tsx

app/features/admin/partials/edit_user.html

app/features/profile/ChangePasswordForm.tsx
```



可以直接参考！：https://blog.csdn.net/qq_29860591/article/details/108634571



