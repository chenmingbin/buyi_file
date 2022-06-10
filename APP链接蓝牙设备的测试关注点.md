## APP连接蓝牙设备的测试关注点

> 在安卓系统上安装“BLE调试助手”，iOS系统上安装“lightblue”，开启后，将手机和手环放置，查看蓝牙的RSSI值是否在-50dB内。

> 蓝牙测试简介地址：https://www.csdn.net/tags/NtTaUg5sOTM1MjAtYmxvZwO0O0OO0O0O.html
>
> 蓝牙调试APP地址：https://blog.csdn.net/weixin_42328389/article/details/124618752
>
> 蓝牙功能测试点地址：https://www.likecs.com/show-340564.html

介绍了蓝牙的基础知识、APP连接蓝牙设备的开发实现，接下来将介绍APP连接蓝牙设备这一过程测试中需要关注的测试点（由于每种蓝牙设备连接需求不同，开发实现上也会有细小差别，实际测试中可视具体开发逻辑进行调整）：

1. APP扫描设备

   ①手机端蓝牙不同设置下是否能正常扫描：蓝牙打开、蓝牙关闭；

   ②蓝牙设备被扫描到并出现在可连接设备列表的条件：蓝牙设备名称与可连接的设备列表中的名称匹配、设备处于广播状态；

   蓝牙设备与可连接的设备列表中名称是否匹配：匹配、不匹配；

   蓝牙设备状态（视具体硬件而定）：待机状态、广播状态、已连接状态、蓝牙设备操作中、数据传输中、关机状态；

   测试以上各种组合场景下，蓝牙设备是否能正常扫描且出现在可连接设备列表；

   ③不同场景下，是否正常扫描：

   一个手机同时扫描多个设备；

   多个手机同时扫描同一个设备；

   多个手机同时扫描多个设备；

   ④扫描过程中取消；

   ⑤扫描结果：扫描超时；未扫描到可连接设备，是否支持重试；扫描失败；扫描成功；

2. 连接设备（APP端点击可连接设备列表，连接蓝牙设备）

   ①测试蓝牙设备状态变化后，不同状态下是否可正常连接：待机状态、广播状态、已连接状态、蓝牙设备操作中、数据传输中、关机状态；

   ②连接结果的处理：连接成功、连接失败、连接超时等；

   ③连接成功后的操作：APP其他操作、连接中断、退到后台、杀掉APP等；

   ④其他场景：扫描到多个设备；

3. 断开连接


   ①设备端断开：设备关机、设备电量耗尽；

   ②APP端断开：手动断开、其他操作断开连接；

4. 其他交互操作


   ①切换其他蓝牙设备，是否正常连接；

   ②蓝牙断开后重新打开，支持自动连接；

   ③距离超出蓝牙可检测范围，是否会断开连接，再恢复到可检测范围，能否自动连接；

   ④手机端连接多个不同蓝牙设备，各个设备功能是否都正常使用；

5. 连接成功后的数据传输


   ①功能上，APP端和蓝牙设备端是否符合正常使用需求；

   ②性能上，APP与蓝牙设备的交互是否满足需求；

## 拓展-蓝牙基础知识

> 1. iOS台下蓝牙开发可以使用 MFI（ExternalAccessory 框架） 或 BLE (CoreBluetooth 框架) 进行，但实际开发中基本都使用 CoreBluetooth 框架，因为它功能更强大，支持蓝牙4.0标准。
>
> 2. 蓝牙4.0 BLE (Bluetooth low energy) ：它的优点在于传输快，耗电低，但传输数据有限，虽然这个传输字节大小硬件工程师可调，但也不会太大。
>
> 3. CoreBluetooth框架的核心是peripheral和 central, 可以理解成外设和中心，发起连接的是central，被连接的设备为 peripheral，它们是一组相对概念。比如，当手机去连接控制蓝牙耳机时，你的手机就是central，当手机蓝牙被另一个手机连接并为其提供服务时就是peripheral。
>
> 4. Service和Characteristic：蓝牙设备通过GATT协议定义的数据通讯方式。一个 peripheral可以提供多种 服务Service，一种Service 又可以包含多个不同的 特征Characteristic。特征就是具体键值对，提供数据的地方。每个特征属性分为读、写、通知等几种方式。
>
> 5. central通过peripheral 的 Characteristic 来读写外设的数据，和获取通知。Peripheral广播自己的Service和characteristic，Central订阅某一个具体的characteristic，Peripheral就和Central之间通过characteristic建立了一个双向的数据通道
>
> 6. 外设peripheral、服务Service、特征characteristic之间的关系：
> 7. UUID：蓝牙上的唯一标示符，为了区分不同服务和特征，就用UUID来表示。

![image-20220610095525755](C:\Users\不易\AppData\Roaming\Typora\typora-user-images\image-20220610095525755.png)

## 拓展-蓝牙的两种工作模式‍

1. 中心模式
   1. 建立中心
   2. 扫描外设（discover）
   3. 连接外设(connect) 连接失败、连接断开、连接成功
   4. 扫描外设中的服务和特征(discover)
      4.1 获取外设的 services
      4.2 获取外设的 Characteristics,获取Characteristics的值，获 Characteristics的 Descriptor 和Descriptor 的值
   5. 与外设做数据交互(explore and interact)
   6. 订阅 Characteristic 的通知
   7. 断开连接(disconnect)
2. 外设模式
   1. 启动一个 Peripheral 管理对象
   2. 本地 Peripheral 设置服务,特性,描述，权限等等
   3. Peripheral 发送广播
   4. 设置处理订阅、取消订阅、读 characteristic、写 characteristic 的委托方法
3. 蓝牙设备的工作状态
   1. 准备（standby）
   2. 广播（advertising）
   3. 监听扫描（Scanning
   4. 发起连接（Initiating）
   5. 已连接（Connected）



## 拓展-蓝牙连接的开发实现

```
这里以手机端作为central，以蓝牙设备作为peripheral，蓝牙设备连接的开发实现主要包括以下步骤：
1. 导入苹果系统蓝牙框架
#import

2. 遵循两个蓝牙框架相关的协议
<cbcentralmanagerdelegate,cbperipheraldelegate></cbcentralmanagerdelegate,cbperipheraldelegate>

3. 新建两个实例属性，一个特征属性
@property (nonatomic, strong) CBCentralManager*centralManager; //中心管理者
@property (nonatomic, strong) CBPeripheral *peripheral; //连接到的外设
@property (nonatomic, strong) CBCharacteristic*characteristic; //特征

4. 初始化CBCentralManager，进行蓝牙管理
- (void)viewDidLoad {
[superviewDidLoad];
self.centralManager= [[CBCentralManager alloc] initWithDelegate:selfqueue:dispatch_get_main_queue()]; //创建实例进行蓝牙管理
}
//若中心管理者初始化之后就会触发下面这个代理方法。该代理方法是用来判断手机蓝牙的状态的
-(void)centralManagerDidUpdateState:(CBCentralManager *)central {
//蓝牙可用，开始扫描外设
if(central.state == CBManagerStatePoweredOn) {
NSLog(@"蓝牙可用");
//在中心管理者成功开启之后再进行一些操作
//搜索扫描外设
//根据SERVICE_UUID来扫描外设，如果不设置SERVICE_UUID，则扫描所有蓝牙设备
//[self.centralManagerstartAdvertising:@{CBAdvertisementDataServiceUUIDsKey:@[[CBUUIDUUIDWithString:SERVICE_UUID]]}];
[centralscanForPeripheralsWithServices:nil options:nil];
}
if(central.state== CBManagerStateUnsupported) {
NSLog(@"该设备不支持蓝牙");
if(central.state == CBManagerStatePoweredOff) {
NSLog(@"蓝牙已关闭");
if(central.state == CBManagerStateUnknown) {
NSLog(@"蓝牙当前状态不明确");
if(central.state == CBManagerStateUnauthorized) {
NSLog(@"蓝牙未被授权");

5. 搜索外围设备

//执行扫描动作之后，如果扫描到外设了，就会自动回调下面的协议方法
/** 发现符合要求的外设，回调 */
- (void)centralManager:(CBCentralManager*)central didDiscoverPeripheral:(CBPeripheral *)peripheraladvertisementData:(NSDictionary
NSLog(@"%@====",peripheral.name);
//根据外设名字有选择性的筛选连接蓝牙设备
if([peripheral.name hasPrefix:@"TEAMOSA"]) {
//在这里对外设携带的广播数据进行进一步的处理
if([self.peripheraNames containsObject:peripheral.name]) {
//如果数组中包含了就不再添加
return;
}

//添加到外设名字数组中
[self.peripheraNamesaddObject:peripheral.name];

//标记外设，让它的生命周期与控制器的一致
self.peripheral= peripheral;

//可以根据外设名字来过滤外设
//[central connectPeripheral:peripheral options:nil];

//连接外设

6. 连接外围设备
//连接外围设备，中心管理者连接外设成功，如果连接成功就会回调这个协议方法

/** 连接成功 */­­­
- (void)centralManager:(CBCentralManager*)central didConnectPeripheral:(CBPeripheral *)peripheral{

//连接成功之后，可以进行服务和特性的发现。停止中心管理设备的扫描动作，要不然在你和已经连接好的外设进行数据沟通时，如果又有一个外设进行广播且符合你的连接条件，那么你的iOS设备也会去连接这个设备（因为iOS BLE4.0是支持一对多连接的），导致数据的混乱。
//停止扫描动作
[self.centralManagerstopScan];

//设置外设的代理
peripheral.delegate= self;

//根据UUID来寻找服务
//[peripheral discoverServices:@[[CBUUID UUIDWithString:SERVICE_UUID]]];

//外设发现服务，传nil代表不过滤，一次性读出外设的所有服务
[peripheraldiscoverServices:nil];

NSLog(@"%s,line = %d, %@=连接成功", __FUNCTION__, __LINE__,peripheral.name);

//外设连接失败
/** 连接失败的回调 */
- (void)centralManager:(CBCentralManager*)central didFailToConnectPeripheral:(CBPeripheral *)peripheral error:(NSError*)error {
NSLog(@"%s,line = %d, %@=连接失败", __FUNCTION__, __LINE__,peripheral.name);

//丢失连接 掉线
/** 断开连接 */
- (void)centralManager:(CBCentralManager*)central didDisconnectPeripheral:(CBPeripheral *)peripheral error:(nullableNSError *)error {
NSLog(@"%s,line = %d, %@=断开连接", __FUNCTION__, __LINE__,peripheral.name);
//断开连接可以设置重新连接
[centralconnectPeripheral:peripheral options:nil];

以上，已经实现了APP端连接蓝牙设备的功能，蓝牙设备连接完成后，还可以对设备进行数据读写，包括：获取外围设备服务和特征；从外围设备读取数据；向外围设备发送（写入）数据等操作。这里暂不具体展开。
```

