# kubernetes集群客户端命令 kubectl

# 一、kubectl命令帮助

集群中的管理操作几乎都可以使用`kubectl`命令完成

```powershell
[root@k8s-master1 ~]# kubectl -h
```



# 二、kubectl命令说明

![image-20260425192618220](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425192618282.png)





![image-20260425192639675](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260425192639717.png)





# 三、kubectl命令补全



~~~powershell
yum install -y bash-completion
source /usr/share/bash-completion/bash_completion
source <(kubectl completion bash)
kubectl completion bash > ~/.kube/completion.bash.inc
source '/root/.kube/completion.bash.inc'  
source $HOME/.bash_profile
~~~

