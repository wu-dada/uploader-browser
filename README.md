# uploader-browser
A lightweight and configurable loader plugin, it supports multiple file upload, file filtering, block upload, queue, progress tracking and other support

# 多文件分片断点续传

## Constructor

### Uploader(arg)

> 适用于单文件分片断点续传

##### Arguments

- `url:string`：上传接口地址
- `file:File`：上传的文件
- `config`：上传配置
  - `retryCount:number`：上传失败后的重试次数；默认 `3 `（即上传失败后最多重试两次）
  - `concurrentLimit:number`：分片上传的并发请求量，默认为 `3` 
  - `chunkSize:number`：分片的大小，默认 `2 * 1024 * 1024`，等于`2MB`
  - `forceDirect:boolean`：上传强制采用直传方式，默认 `false
- `putExtra`：自定义参数配置
  - `fname:string`：可以覆盖`file[name]`
  - `params:object`：放置自定义参数

##### Properties

- `uuid:string`：实例的唯一ID
- `file:File`：当前文件
- `status:STATUS`：实例状态


##### Methods

- `start()` ：开始上传
- `stop()` ：停止上传，再次执行 `start()` 会继续上传
- `reset()` ：重置断点续传的记录，从分片 `0` 重新上传

##### Events

- `next` ：文件上传时触发。使用此事件来更新当前文件的上传进度。返回值：`Progress`
- `complete` ：成功上传文件时触发。返回值：接口的响应数据
- `error` ：发生错误时触发。返回值：包含错误消息，有时还包含文件和其他详细信息

---

### uploaderMultiple(url,config)

> 适用于多文件分片断点续传

##### Arguments

- `url:string`：上传接口地址
- `config`：上传配置
  - `retryCount:number`：上传失败后的重试次数；默认 `3 `（即上传失败后最多重试两次）
  - `concurrentLimit:number`：分片上传的并发请求量，默认为 `3` 
  - `chunkSize:number`：分片的大小，默认 `2 * 1024 * 1024`，等于`2MB`
  - `forceDirect:boolean`：上传强制采用直传方式，默认 `false

##### Properties

- `files:Array<FileInfo>`：队列

##### Methods

- `addFile(file,putExtra)` ：将文件添加到队列中  
- `getFile(id)` ：通过 `id` 返回指定的文件对象。返回值 `fileInfo`
- `removeFile(id)` ：通过 `id` 删除指定的文件。返回值 `boolean`
- `start()` ：开始队列中的文件
- `stop()` ：停止队列，再次执行 `start()` 会继续按队列处理
- `pause(id)` ：通过 `id` 暂停或继续指定的文件。返回值 `boolean`

##### Events

- `UploadProgress` ：文件上传时触发。使用此事件来更新当前文件的上传进度。返回值：`Progress` 和 `FileInfo`
- `UploadComplete` ：队列中的所有文件处理完时触发。返回值：`Array<FileInfo>`
- `FileUploaded`：成功上传文件时触发。返回值：接口的响应数据和 `FileInfo`
- `FileUploadError`：发生错误时触发。返回值：包含错误消息，有时还包含文件和其他详细信息

---


## interface

##### Progress

- `total:object`：整个文件的上传进度
  - `loaded:number`：已上传的字节
  - `total:number`：总共的字节
  - `percent:number`：百分比，`loaded / total* 100`
- `chunks:Array<total>`：分片的上传进度，以数组方式返回 `total:object` 

##### PutExtra：自定义参数配置

- `fname:string`：可以覆盖`file[name]`
- `params:object`：放置自定义参数

##### FileInfo：文件信息

   - `id:string`：唯一ID
   - `name:string`:：文件名
   - `type:string`:：文件类型
   - `loaded:number`:：已传字节
   - `total:number`:：总字节
   - `percent:number`:：百分比
   - `status:number`:：状态
