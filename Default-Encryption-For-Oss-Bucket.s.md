# 服务器端加密编码 

OSS默认加密提供了一种方法来设置存储空间的默认加密行为。您可以对存储空间设置默认加密，从而对其中存储的对象进行加密。这些对象使用京东云的KMS 托管密钥 (SSE-KMS) 通过服务器端加密进行加密。

在使用服务器端加密时，OSS 在将对象保存到其数据中心的磁盘上之前对其进行加密，并在下载对象时对其进行解密。有关使用服务器端加密和加密密钥管理来保护数据的更多信息，请参阅使用加密保护数据。
## OSS服务端加密主要应用场景 

OSS通过服务端加密机制，提供静态数据保护。适合于对于文件存储有高安全性或者合规性要求的应用场景。例如，深度学习样本文件的存储、在线协作类文档数据的存储。针对不同的应用场景，OSS有如下几种服务端加密方式：

-   使用KMS托管密钥进行加解密

    用户上传文件时，可以使用指定的CMK ID或者默认KMS托管的CMK进行加解密操作。这种场景适合于大量的数据加解密。用户数据无需通过网络发送到KMS服务端进行加解密，这是一种低成本的加解密方式。

-   使用OSS完全托管加密

    基于OSS完全托管的加密方式，是Object的一种属性。用户创建Object时，只需在put object 请求中携带x-oss-server-side-encryption的HTTP Header，并指定其值为AES256，即可以实现该Object的服务器端加密存储。该方式适合于批量数据的加解密。


## 服务端加密选项

OSS服务端加密有以下选项，用户可根据需要（密钥管理策略）进行选择：

-   使用由OSS完全托管的服务端加密功能：数据加密密钥的生成和管理，由OSS负责，并采用高强度、多因素的安全措施进行保护。数据加密的算法采用使用行业标准的强加密算法AES-256（即256位高级加密标准）。

## 服务端加密-OSS完全托管 


## 服务端加密-KMS托管主密钥 {#section_awd_2md_5db .section}

当用户创建Object时，在请求中携带`x-oss-server-side-encryption`的HTTP Header，并指定其值为`KMS`，即可实现在服务端加密存储该Object（密钥管理使用KMS服务）。OSS通过KMS服务，采用信封加密机制、AES-256加密算法等强安全方法，对用户存储在OSS的对象进行加密保护。

KMS是阿里云提供的一款安全、易用的管理类服务。用户无需花费大量成本来保护密钥的保密性、完整性和可用性，借助密钥管理服务，用户可以安全、便捷的使用密钥，专注于开发加解密功能场景。用户可以通过KMS控制台中查看和管理KMS密钥。

以下操作支持在请求中携带这些HTTP Header

-   PutObject：简单上传
-   CopyObject：复制Object
-   InitiateMultipartUpload：分片上传

**说明：** 

-   使用该模式，首次向某地域中的Bucket添加加密对象时，将自动为您在该地域创建一个默认KMS密钥（用作用户主密钥CMK）。此密钥将用于服务端加密（KMS模式）。
-   您可以在KMS控制台查看该密钥信息，请参见[KMS使用文档](https://www.alibabacloud.com/help/doc-detail/28943.htm)。

## 服务端加密API使用 {#section_mbf_hmd_5db .section}

**操作请求**

以下API支持请求携带`x-oss-server-side-encryption`头：

-   PutObject：简单上传
-   CopyObject：复制Object
-   InitiateMultipartUpload：分片上传

HTTP Header格式如下：

|名称|描述|示例|
|:-|:-|:-|
|x-oss-server-side-encryption|指定服务端加密的模式有效值：AES256、KMS

|`x-oss-server-side-encryption:KMS`表示使用服务端加密模式为KMS托管主密钥|

**说明：** 

-   其他OSS收到的请求中，如果出现`x-oss-server-side-encryption`头，OSS会直接返回HTTP状态码400，并在消息体内注明错误码为InvalidArgument。
-   如果您指定的`x-oss-server-side-encryption`头不为有效值，OSS会直接返回HTTP状态码400，并在消息体内注明错误码为`InvalidEncryptionAlgorithmError`。

**操作响应**

通过服务器端加密存储的Object，以下API请求中OSS会返回`x-oss-server-side-encryption`头：

-   PutObject
-   CopyObject
-   InitiateMultipartUpload
-   UploadPart
-   CompleteMultipartUpload
-   GetObject
-   HeadObject

**Meta信息**

通过服务器端加密-KMS托管主密钥模式存储的Object，其Meta信息会增加以下字段：

|名称|描述|示例|
|:-|:-|:-|
|x-oss-server-side-encryption|指定服务器端加密的模式|`x-oss-server-side-encryption: KMS`|
|x-oss-server-side-encryption-key-id|该Object加密所用的用户KMS key的ID|`x-oss-server-side-encryption-key-id: 72779642-7d88-4a0f-8d1f-1081a9cc7afb`|

**相关API**

-   [AppendObject](../../../../intl.zh-CN/API 参考/关于Object操作/AppendObject.md#)
-   [PutObject](../../../../intl.zh-CN/API 参考/关于Object操作/PutObject.md#)
-   [CopyObject](../../../../intl.zh-CN/API 参考/关于Object操作/CopyObject.md#)
-   [PostObject](../../../../intl.zh-CN/API 参考/关于Object操作/PostObject.md#)
