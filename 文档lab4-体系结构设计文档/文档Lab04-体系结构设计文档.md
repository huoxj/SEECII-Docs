# 文档Lab04-体系结构设计文档

## **文档作者**

主要编写者：史创屹、杨枫、刘存玺

其他编写者：董天诺

## **文档修改历史**

| 修改人员 | 日期 | 修改原因 | 版本号 |
| --- | --- | --- | --- |
| 刘存玺 | 2024-4-17 | 根据已经开发的系统完善文档5.1 5.2 | v0.1 |
| 史创屹 | 2024-4-17 | 根据已经开发的系统完善文档1到4 | v0.2 |
| 杨枫 | 2024-4-17 | 根据已经开发的系统完善文档5.3及之后所有的内容 | v1.0 |
| 刘存玺 | 2024-6-1 | 根据已经开发的系统完善文档的开发包图 | v2.0 |

## **1. 引言**

### 1.1 编制目的

- 本报告详细完成对 **蓝鲸网购** 的概要设计，达到指导详细设计和开发的目的，同时实现和测试人员及用户的沟通。
- 本报告面向开发人员、测试人员及最终用户而编写，是了解系统的导航。

### **1.2 词汇表**

| 词汇名称 | 词汇含义 | 备注 |
| --- | --- | --- |
| Impl | Implementation | 对业务逻辑层接口的具体实现 |
| oss | Object Storage Service | 对象存储服务 |

### **1.3 参考资料**

- 丁二玉 刘钦《软件工程与计算 卷2 软件开发的技术基础 》
- 用例文档
- 需求规格说明书

## **2. 产品概述**

- 参考用例文档以及需求规格说明书中对系统的概括描述

## **3. 逻辑视角**

- Bluewhale网购平台系统采用浏览器和服务器架构模式，前端的展示层通过HTTP REST API与后端进行通信。后端的开发选用分层体系结构风格，包括接口层、业务逻辑层、数据访问层。整个系统的逻辑视角分为3层（展示层、业务逻辑层、数据访问层）。

- **分层体系结构的逻辑视角图**

    ![分层体系结构的逻辑视角图.drawio](drawio/分层体系结构的逻辑视角图.drawio.svg)

- **软件体系结构逻辑设计方案**

![屏幕截图 2024-04-17 152707.png](drawio/分层体系结构的逻辑设计方案图.drawio.svg)

## 组合视角

### **4.1 开发包图**

- 开发包设计

| 开发(物理)包 | 依赖的其他开发包 |
| --- | --- |
| user(UI) | api,router |
| UserController | UserService,vo |
| UserService | vo |
| UserServiceImpl | UserService,po,vo,UserRepository,InviteCodeRepository,SecurityUtil,TokenUtil |
| UserRepository | po |
| user(data) | / |
| store(UI) | api,router |
| StoreController | StoreService,vo |
| StoreService | vo |
| StoreServiceImpl | StoreService,po,vo,StoreRepository |
| StoreRepository | po |
| store(data) | / |
| order(UI) | api,router |
| OrderController | OrderService,vo |
| OrderService | vo |
| OrderServiceImpl | OrderService,po,vo,OrderRepository,SecurityUtil,TokenUtil |
| OrderRepository | po |
| order(data) | / |
| item(UI) | api,router |
| ItemController | ItemService,po,vo |
| ItemService | po,vo |
| ItemServiceImpl | ItemService,po,vo,UserRepository,ItemRepository,SecurityUtil,TokenUtil |
| ItemRepository | po |
| item(data) | / |
| image(UI) | api,router |
| ImageController | ImageService,vo |
| ImageService | po,vo |
| ImageServiceImpl | ImageService,po,vo,ImageRepository,OssUtil |
| ImageRepository | po |
| image(data) | / |
| comment(UI) | api,router |
| CommentController | CommentService,vo |
| CommentService | vo |
| CommentServiceImpl | CommentService,po,vo,repository,SecurityUtil,TokenUtil |
| CommentRepository | po |
| comment(data) | / |
| InviteCodeRepository | po |
| api | / |
| router | / |
| po | / |
| vo | / |
| OssUtil | / |
| SecurityUtil | / |
| TokenUtil | / |
| enums | / |
| exception | / |
- **客户端开发包图**

![屏幕截图 2024-04-17 163703.png](drawio/客户端开发包图.drawio.svg)

- **服务端开发包图**

![屏幕截图 2024-04-17 170443.png](drawio/服务端开发包图.drawio.svg)

**4.2 运行时进程**

- 在蓝鲸商城网购平台系统中，会有多个客户端进程和一个服务端进程，如下图所示

![屏幕截图 2024-04-17 143109.png](drawio/4.2-1.drawio.svg)

### **4.3 物理部署**

在客户端，用户只需要和浏览器进行交互。本系统支持常见浏览器以及操作系统。

在服务端节点上需要通过Docker拉取Node.js的镜像、Maven镜像以及MySQL server镜像，具体版本要求已在下图中展示。前端通过npm部署到Node.js镜像中，后端通过Maven构建并部署到对应镜像中。

- **部署图**

![屏幕截图 2024-04-17 172333.png](drawio/部署图.drawio.svg)

## **接口视角**

### **5.1 模块的职责**

- **模块视图**

![5.1-1.drawio.svg](drawio/模块视图.drawio.svg)

- 客户端各层职责

| 层 | 职责 |
| --- | --- |
| 用户界面层 | 基于web的网购商城客户端界面 |
| 客户端网络模块 | 向服务器发送HTTP REST网络请求 |
- 服务端各层职责

| 层 | 职责 |
| --- | --- |
| 接口层Controller | 负责响应客户端传来的HTTP REST网络请求 |
| 业务逻辑层Service/ServiceImpl | 负责接收并处理来自接口层的请求 |
| 数据接口层Repository | 负责数据的持久化和数据访问接口 |
- 层之间调用接口，下表为一个例子

| 接口 | 服务调用方 | 服务提供方 |
| --- | --- | --- |
| UserController | 客户端网络模块 | 业务逻辑层 |
| UserRepository | 业务逻辑层 | 数据接口层 |

![5.1-2.drawio.svg](drawio/5.1-2.drawio.svg)

### **5.2 用户界面层的分解**

根据需求，系统存在14个用户界面

界面的跳转如下图所示：

![5.2.1.drawio.svg](drawio/页面跳转图.drawio.svg)

用户界面层的开发包图

![展示层开发包图.drawio](images/展示层开发包图.drawio-17178167374441.svg)

### **5.2.1 职责**

以storeUI的职责为例

| 模块 | 职责 |
| --- | --- |
| views/store/StoreDetail.vue | 展示商店的详情信息以及商品列表，包括相关逻辑的处理 |
| views/store/CreateStore.vue | 创建商店的用户界面 |

### **5.2.2 接口规范**

以storeAPI的职责为例(api/store.ts)

| 名称 | 语法 | 备注 |
| --- | --- | --- |
| 请求商品信息列表 | reqStoreInfoList()                                           | 返回所有商店的信息 |
| 请求某商店信息   | reqStoreInfo({storeId:number})                               |                    |
| 请求创建商店     | reqStoreCreate(storeInfo:{name: string, description: string}) |                    |

需要的服务接口如下表所示

| 服务名 | 服务 |
| --- | --- |
| controller.StoreController | 商店相关请求的接口层接口类 |

### **5.3 业务逻辑层的分解**

业务逻辑层包括多个针对界面的业务逻辑处理对象。例如，UserService对象负责处理登录界面的业务逻辑；StoreService对象负责处理商店页面的业务逻辑；ItemService对象负责处理商品页面的业务逻辑；OrderService对象负责处理订单页面的业务逻辑；CommentService对象负责处理评论页面的业务逻辑。业务逻辑层的设计图如下所示：

![5.3-1.drawio.svg](drawio/5.3-1.drawio.svg)

### **5.3.1 职责**

| 模块 | 职责 |
| --- | --- |
| UserService | 负责实现对应与登陆界面所需要的服务 |
| StoreService | 负责实现商店页面所需要的服务 |
| ItemService | 负责实现商品页面所需要的服务 |
| OrderService | 负责实现订单页面所需要的服务 |
| CommentService | 负责实现评论界面所需要的服务 |
| ImageService | 负责实现所有页面的图片服务 |

### **5.3.2 接口规范**

5.3.2.1 UserService模块的接口规范

提供的服务（供接口）

| 名称 | 语法 | 前置条件 | 后置条件 |
| --- | --- | --- | --- |
| UserService.register | public Boolean register(UserVO userVO) | 注册信息符合输入规则 | 新建一个持久化对象User |
| UserService.login | public String login(String phone,String password) | 电话和密码信息符合输入规则 | 查找是否存在对应的User，根据输入的密码返回登录令牌 |
| UserService.getInformation | public UserVO getInformation(); | 无 | 调用数据库返回用户VO |
| UserService.updateInformation | public Boolean updateInformation(UserVO userVO); | 用户更新信息符合规则 | 更新数据库中持久对象的内容 |
| UserService.getInviCode | public String getInviteCode(RoleEnum role); | role是合法角色 | 返回一个邀请码 |

需要的服务（需接口）

| 服务名 | 服务 |
| --- | --- |
| UserRepository.findByPhone(String phone) | 根据phone查找单一持久化对象 |
| UserRepository.save(User user) | 保存单一持久化对象 |
| UserRepository.findByPhoneAndPassword(String phone,String password) | 根据phone和password查找单一持久化对象 |
| InviteRepository.findByCode(String code) | 根据code查找单一持久对象 |
| InviteRepository.delete(String code) | 删除单一持久化对象 |
| TokenUtil.getToken(User user) | 获取用户令牌 |
| SecurityUtil.getCurrentUser() | 获取当前登录用户信息 |
| InviteCodeRepository.save(InviteCode inviteCode) | 保存单一持久化对象 |

5.3.2.2 StoreService模块的接口规范

提供的接口(供接口)

| 名称 | 语法 | 前置条件 | 后置条件 |
| --- | --- | --- | --- |
| StoreService.getStores | public List<StoreVO> getStores() | 无 | 返回所有商店VO |
| StoreService.getStore | public StoreVO getStore(int storeId) | 提供的商店id合法有效 | 返回对应商店VO |
| StoreService.createStore | public Integer createStore(StoreVO storeVO) | 输入商店信息符合规则 | 创建一个持久化商店对象 |
| StoreService.deleteStore | public boolean deleteStore(int storeId) | 提供的商店id合法有效 | 删除对应的持久化商店对象 |

需要的接口(需接口)

| 服务名 | 服务 |
| --- | --- |
| StoreRepository.findAll() | 查找Store表中所有持久化对象 |
| StoreRepository.findById(int storeId) | 根据商店id查找单一持久化对象 |
| StoreRepository.findByName(String name) | 根据商店名称查找单一持久化对象 |
| StoreRepository.save(Store store) | 保存单一持久化对象 |
| StoreRepository.delete(Store store) | 删除单一持久化对象 |

5.3.2.3 Item模块的接口规范

提供的接口(供接口)

| 名称 | 语法 | 前置条件 | 后置条件 |
| --- | --- | --- | --- |
| ItemService.appendItem | public Integer appendItem(ItemVO itemVO) | 商品信息符合规则 | 创建持久化商品对象并返回商品id |
| ItemService.getItemByItemId | public ItemVO getItemById(Integer itemId) | 输入的商品id合法有效 | 返回对应商品VO |
| ItemService.getItemListByStoreId | public List<Item> getItemListByStoreId(Integer storeId) | 输入的商店id合法有效 | 返回该商店所有商品VO |
| ItemService.updateInventory | public Boolean updateInventory(Integer itemId, Integer inventory) | 输入的商品id与库存数量合法有效 | 更新持久化对象信息 |
| ItemService.getItemCategories | public List<CategoryEnum> getItemCategories() | 无 | 返回商品分类的枚举类信息 |

需要的接口(需接口)

| 服务名 | 服务 |
| --- | --- |
| ItemRepository.save(item) | 保存单一持久化对象 |
| ItemRepository.findById(int id) | 根据商品id查找单一持久化对象 |
| ItemRepository.findByStoreId(int storeId) | 根据商店id查找多个持久化对象 |
| SecurityUtil.getCurrentUser() | 获取当前用户信息 |

5.3.2.4 Order模块的接口规范

提供的接口(供接口)

| 名称 | 语法 | 前置条件 | 后置条件 |
| --- | --- | --- | --- |
| OrderService.getById | public OrderVO getById(int orderId) | 输入的订单id合法有效 | 返回对应订单的VO |
| OrderService.getAll | public List<OrderVO> getAll() | 无 | 返回所有订单的VO |
| OrderService.getByUserId | public List<OrderVO> getByUserId() | 无 | 返回当前用户所有订单VO |
| OrderService..getByStoreId | public List<OrderVO> getByStoreId() | 无 | 返回当前用户所属商店的所有订单VO |
| OrderService.createOrder | public Integer createOrder(OrderVO orderVO) | 输入的订单信息合法有效 | 创建一个单一订单持久化对象并返回订单id |
| OrderService.deleteOrder | public Boolean deleteOrder(int orderId) | 输入的订单id合法有效 | 删除单一订单持久化对象 |
| OrderService.purchaseOrder | public Boolean purchaseOrder(int orderId) | 输入的订单id合法有效且订单状态为未付款 | 更新单一订单持久化对象状态为未发货 |
| OrderService.sendOrder | public Boolean sendOrder(int orderId) | 输入的订单id合法有效且状态为未发货 | 更新单一订单持久化对象状态为已发货 |
| OrderService.receiveOrder | public Boolean receiveOrder(int orderId) | 输入的订单id合法有效且订单状态为未收货 | 更新单一订单持久化对象状态为已收货 |
| OrderService.commentOrder | public Boolean commentOrder(int orderId) | 输入的订单id合法有效且订单状态为未发货 | 更新单一订单持久化对象状态为已评论 |

需要的接口(需接口)

| 服务名 | 服务 |
| --- | --- |
| OrderRepository.findById(int id) | 根据订单id查找单一持久化对象 |
| OrderRepository.findAll() | 查找所有订单持久化对象 |
| OrderRepository.findByUserId(int userId) | 根据用户id查找多个持久化对象 |
| OrderRepository.findByStoreId(int storeId) | 根据商店id查找多个持久化对象 |
| OrderRepository.save(Order order) | 保存单一持久化对象 |
| OrderRepository.delete(Order order) | 删除单一持久化对象 |
| SecurityUtil.getCurrentUser() | 获取当前用户信息 |

5.3.2.5 Comment模块的接口规范

提供的接口(供接口)

| 名称 | 语法 | 前置条件 | 后置条件 |
| --- | --- | --- | --- |
| CommentService.getCommentById | public CommentVO getCommentById(Integer commentId) | 输入的id是合法有效的 | 返回对应的评论VO |
| CommentService.getCommentByItemAndCount | public List<CommentVO> getCommentByItemIdAndCount(Integer itemId, Integer startInd) | 输入的商品id和index是合法有效的 | 返回对应商品对应数量的评论VO |
| CommentService.getCommentByItemId | public Integer getCommentCountByItemId(Integer itemId) | 输入的商品id是合法有效的 | 返回对应商品的所有评论VO |
| CommentService.appendComment | public Integer appendComment(CommentVO commentVO) | 输入的评论信息是合法有效的 | 创建持久化对象,持久化更新对应商品及商店的评分,并返回该评论的id |
| CommentService.updateItemRating | private void updateItemRating(Integer itemId, float rating) | 输入的商品id以及新增评价的评分是合法有效的 | 持久化更新该商品评分 |
| CommentService.updateStoreRating | private void updateStoreRating(Integer itemId, float rating) | 输入的商店id以及新增评价的评分是合法有效的 | 持久化更新该商店评分 |

需要的接口(需接口)

| 服务名 | 服务 |
| --- | --- |
| CommentRepository.findById(int id ) | 根据评论id查找单一持久化对象 |
| CommentRepository.findByItemId(int ItemId ) | 根据商品id查找多个持久化对象 |
| CommentRepository.save(Comment comment) | 保存单一持久化对象 |
| SecurityUtil.getCurrentUser() | 查询当前用户信息 |
| ItemRepository.findById(int id) | 根据商品id查找单一持久化对象 |
| ItemRepository.findByStoreId(int id) | 根据商店id查找多个持久化对象 |
| ItemRepository.save(Item item) | 保存单一持久化对象 |
| StoreRepository(Store store) | 保存单一持久化对象 |

5.3.2.6 Image模块的接口规范

提供的接口(供接口)

| 名称 | 语法 | 前置条件 | 后置条件 |
| --- | --- | --- | --- |
| ImageService.upload | public String upload(MultipartFile file, ImageType type, int parentId, int ind) | 输入的图片信息有效 | 将图片上传至oss并且更新image数据库 |
| ImageService.delete | public Boolean delete(ImageType type, int parentId) | 要删除的图片信息存在 | 同时删除oss和数据库中的图片信息 |
| ImageService.download | public List<ImageVO> download(ImageType type, int parentId) | 要下载的图片信息存在 | 读取数据库中图片的url并返回 |

需要的接口(需接口)

| 服务名 | 服务 |
| --- | --- |
| OssUtil.upload(String objectName,InputStream inputStream) | 上传图片到oss |
| OssUtil.delete(String objectUrl) | 根据url删除oss中的图片 |
| ImageService.findAllByTypeAndParentId( ImageType type,int parentId) | 根据图片类型和父辈id查找多个持久化对象 |
| ImageService.save(Image image) | 保存单个持久化对象 |
| ImageService.delete(Image image) | 删除单个持久化对象 |

### **5.4 数据层的分解**

数据层主要给业务逻辑层提供数据访问服务,包括对于持久化数据的增、删、改、查，各个业务逻辑需要的服务由其对应的Repository接口提供。

### **5.4.1 职责**

| 模块 | 职责 |
| --- | --- |
| JPARepository | 持久化数据库的接口,提供增、删、改、查的服务 |
| 各模块Repository | 继承自JPARepository，根据逻辑业务需要提供更加精细的增、删、改、查服务。 |

### **5.4.2 接口规范**

提供的服务（供接口）

可以参考JPA Repository接口的规范，以下为UserRepository的例子

| 名称 | 语法 | 前置条件 | 后置条件 |
| --- | --- | --- | --- |
| UserRepository.save | void save( User user ) | 无 | 若数据库无该PO,新建一个,否则更新该PO |
| UserRepository.delete | void delete( User user ) | 数据库存在该PO | 删除一个PO |
| UserRepository.findByPhone | User findByPhone(String phone) | 无 | 按电话号码查询返回相应的UserPO |
| UserRepository.findByPhoneAndPassword | User findByPhoneAndPassword(String phone, String password) | 无 | 按电话号码和密码查询返回相应的UserPO |
| UserRepository.findAll | List<User> findAll() | 无 | 返回所有UserPO |

## **信息视角**

### **6.1 描述数据持久化对象(PO)**

系统的PO类就是对应的相关的实体类。在此只做简单的介绍

- UserPO类包含用户的id、名称、电话号码、密码、创建时间、所属商店id、地址、角色。
  
- StorePO类包含商店的id、名称、描述、评分。
  
- OrderPO类包含订单的id、商品id、用户id、状态、配送、商店id、优惠券、价格、数量、创建时间、发货时间、签收时间。
  
- ItemPO类包含了商品的id、名称、描述、价格、评分、所属类别、所属商店id、库存。
  
- InviteCodePO类包含了邀请码的id、值、被邀请者角色。
  
- CommentPO类包含了评价的id、内容、被评价商品id、评价者id、订单id、商品评分、评价创建时间。
  
- ImagePO类包含了图片的id、url、类型、所属父辈id、排序。
- 以及CouponPO、CouponGroupPo

### **6.2 数据库表**

| Table名称    | 名称       | 对应PO        |
| ------------ | ---------- | ------------- |
| Comment      | 评价表     | CommentPO     |
| Coupon       | 优惠券表   | CouponPO      |
| Coupon_group | 优惠券组表 | CouponGroupPO |
| Image        | 图像表     | ImagePO       |
| Invite_code  | 邀请码表   | InviteCodePO  |
| Item         | 商品表     | ItemPO        |
| Order        | 订单表     | OrderPO       |
| Store        | 商店信息表 | StorePO       |
| User         | 用户信息表 | UserPO        |

