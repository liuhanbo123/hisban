# 1.1. Windows  



## 1.1.1. 快捷键  

| 作用名               | 代码         |
| -------------------- | ------------ |
| 打开服务             | services.msc |
| 设备管理器           | devmgmt.msc  |
| 注册表               | regedit.exe  |
| 系统信息             | winver       |
| 控制面板             | control      |
| 打开服务             | wmimgmt.msc  |
| 控制台               | mmc          |
| 进入远程             | mstsc        |
| 打开本地组策略管理器 | gpedit       |
| 打开组策略           | rsop         |
| 任务计划             | taskschd.msc |



## 1.1.2. Powershell  

- 定义变量

  ```
  $a=1 ;定义变量
  ```

- 查看CPU核心数

  ```
  1. wmic
  	cpu get *
  2. systeminfo
  （1）socket就是主板上插cpu的槽的数目，也即管理员说的”路“
  （2）core就是我们平时说的”核“，即双核，4核等
  （3）thread就是每个core的硬件线程数，即超线程
  具体例子，某个服务器是：2路4核超线程（一般默认为2个线程），那么，通过cat /proc/cpuinfo看到的是2*4*2=16个processor，很多人也习惯成为16核了！
  ```

- 查看设备SN

  ```
  wmic bios get serialnumber
  ```

- 对进程的操作

  ```
  cmd查询所有进程：tasklist
  cmd结束进程：tskill PID
  ```

- 运行powershell脚本

  ```
  C:\> powershell.exe -ExecutionPolicy RemoteSigned   -file "C:\p test.ps1"
  ```

- 获取服务器最后启动时间

  ```
  wmic path Win32_OperatingSystem get LastBootUpTime
  ```

- 对用户的操作

  - 创建用户

    ```
    net user <userName> <password> /add # net user weihanli Admin1234 /add
    ```
    
  - 删除用户

    ```
    net user username /delete # 删除用户名为 username 的用户
    ```

  - 添加用户到组
    
    ```
    net localgroup Administrators # 查看 Administrators 用户组信息
    Net localgroup Administrators "ap\eveli.li" /add # 增加username用户到 Administrators用户组
    ```
    
  - 删除用户属于的组
    
    ```
    net localgroup Administrators username /delete # 从 Administrators 用户组删除 username 用户
    ```
    
  - 查询本地用户最后登录时间
    
    ```
    $adsi = [ADSI]"WinNT://$env:COMPUTERNAME"
    $adsi.Children | where {$_.SchemaClassName -eq 'user'}  | select name,Lastlogin
    ```
    
    
    
  - 



## 1.1.3. GPO  

- 强制更新GPO

  ```
  Gpupdate /force
  ```

- windows patch

  ```
  Configure Automatic Updates: 
  Specify intranet Microsoft update:
  Re-prompt for restart:
  ```

- windows defender

  ```
  Computer Configuration->Admin Template->Windows Components->Windows Defender->Signature Update
  				`Define the order :开启并添加InternalDefinitionUpdateServer|MicrosoftUpdateServer|MMPC
  				`Specify the day :Saturday
  `Specify the time :60  #则为凌晨2点检查更新。
  ```

  

## 1.1.4. WSUS  

- 查看 本地策略 wsus地址

  ```powershell
  Gpedit
  ```

  Administrative templates - windows components - windows update

- 查看 组策略 wsus地址

  ```powershell
  RSOP
  ```

  Administrative templates - windows components - windows update

  查看Specify intranet Microsoft Update service locati

  ​	Setting 设置

  ​	Precedence 查看当前所属策略

- 查看 注册表地址

  ```POWERSHELL
  REGEDIT
  ```

  ​	 HKEY_lOCAL_MACHINE - SOFTWARE - Policies - Microsoft - Windows - WindowsUpdate

  ​	查看WUServer 和 WUStatus Server 的地址是否正确

- 问题处理

  > 1. 长时间未升级的winodws server 2012系统，因为windows update agent版本太旧导致无法通过WSUS更新的问题
  >
  > > 解决方法：
  > >
  > >  先对kb4504418进行操作
  > >
  > >  a. 通过以下命令修复系统： 
  > >
  > > DISM /Online /Cleanup-Image /RestoreHealth
  > >
  > > b. 解压KB4504418 msu 文件到c:\temp 文件夹
  > >
  > > c. 用如下DISM 命令来安KB4504418. 
  > >
  > > Dism /online /add-package /packagepath:c:\temp\Windows8-RT-KB4504418-x64.cab
  > >
  > >  
  > >
  > > 在对kb4507462进行操作
  > >
  > > b. 解压KB4504418 msu 文件到c:\temp 文件夹
  > >
  > > c. 用如下DISM 命令来安KB4504418. 
  > >
  > > Dism /online /add-package /packagepath:c:\temp\Windows8-RT-KB4504418-x64.cab
  > >
  >



## 1.1.5. 实际操作  

- windows server 2016以上安装.net 3.5

  - 找到一个windows server 2016的iso镜像文件拷贝.\soyrces\sxs\全部文件到本地

  - 在添加.net角色时，在如下处设置刚才拷贝文件的路径

    ![net3.5-1](..\images\NET3.5-1.png)

    ![net3.5-1](..\images\NET3.5-2.png)

- windows 加域

