## UTakePhoto简介

* 支持系统拍照，及自定义Intent拍照
* 支持相册选取，及自定义Intent获取
* 支持图片压缩，默认采用鲁班压缩
* 支持图片裁剪，系统裁剪及自带裁剪
* 自动适配camera及sd权限
* 适配AndroidQ
* 链式调用
## 如何使用

```
    UTakePhoto.with(mActivity).openCamera().build(new ITakePhotoResult() {
            @Override
            public void takeSuccess(List<Uri> uriList) {
                
            }

            @Override
            public void takeFailure(TakeException ex) {

            }

            @Override
            public void takeCancel() {

            }
        });
```
或者这样

```
           UTakePhoto.with(mFragment)
                .openAlbum()
                .setCrop(new CropOptions.Builder().create())
                .setCompressConfig(new CompressConfig.Builder().create())
                .build(new ITakePhotoResult() {
                    @Override
                    public void takeSuccess(List<Uri> uriList) {

                    }

                    @Override
                    public void takeFailure(TakeException ex) {

                    }

                    @Override
                    public void takeCancel() {

                    }
                });
```

## 安装说明
Gradle:


```
implementation 'com.sl.utakephoto:uTakePhoto:1.0.4'
```
Maven

```
<dependency>
  <groupId>com.sl.utakephoto</groupId>
  <artifactId>uTakePhoto</artifactId>
  <version>1.0.4</version>
  <type>pom</type>
</dependency>
```
# API
## 基本用法
图片默认储存在context.getExternalFilesDir("Pictures") 目录下，不需要指定的话，一行代码足矣：

```
UTakePhoto.with(mActivity).openCamera().build(new ITakePhotoResult() {
            @Override
            public void takeSuccess(List<Uri> uriList) {
                
            }

            @Override
            public void takeFailure(TakeException ex) {

            }

            @Override
            public void takeCancel() {

            }
        });
```
## 拍照

```
     /**
     * 默认储存在getExternalFilesDir(Pictures)目录下
     *
     * @return
     */
    public TakePhotoManager openCamera() {
        return openCamera(null, null, null);
    }

    /**
     * 打开系统相机，输出路径自定义
     *
     * @param outPutUri 拍照路径
     * @return
     */
    public TakePhotoManager openCamera(Uri outPutUri) {
        return openCamera(outPutUri, null, null);
    }

    /**
     * 打开系统相机，输出路径自定义
     * 在androidQ上建议采用这个方法，因为如果采用传入mediaStore的Uri的方式，会在相册里创建一个空的img
     *
     * @param relativePath androidQ上清单文件中android:requestLegacyExternalStorage="true"
     *                     则relativePath 必须以 Pictures/DCIM 为跟路径；
     *                     Q以下默认根路径是Environment.getExternalStorageDirectory()
     * @return
     */
    public TakePhotoManager openCamera(String relativePath) {
        return openCamera(null, relativePath, null);
    }
```
如果想获取拍照图片的话，传入指定Uri或者使用relativePath两种方式。
relativePath对应androidQ中MediaStore.Images.Media.RELATIVE_PATH，
androidQ以下的以Environment.getExternalStorageDirectory()为根路径。根据需要选择
## 选择照片
默认打开相册Intent

```
     /**
     * 打开相册
     *
     * @return
     */
    public TakePhotoManager openAlbum() {
        return openAlbum(null);
    }

    /**
     * 打开指定相册
     *
     * @param intent 通过Intent跳转的时候，需要返回setResult(RESULT_OK,Intent.setData(Uri)))
     * @return this
     */
    public TakePhotoManager openAlbum(Intent intent) {
        takeType = TYPE_SELECT_IMAGE;
        this.intent = intent;
        return this;
    }
```
## 裁剪

```
CropOptions cropOptions = new CropOptions.Builder()
.setAspectX(1).setAspectY(1)
.setOutputX(100).setOutputY(1)
.setWithOwnCrop(true)//使用系统裁剪还是自带裁剪
.create();
UTakePhoto.with(mActivity).openCamera().setCrop(cropOptions)
```
## 压缩
压缩采用luban压缩，计算inSampleSize，简单压缩，如果不想使用，可以自己拿到图片处理
```
CompressConfig compressConfig = new CompressConfig.Builder()
                .setFocusAlpha(false)//是否支持透明度
                .setLeastCompressSize(200)//最小压缩尺寸
                .setTargetUri()//压缩图片储存路径
                .create();
UTakePhoto.with(mActivity).openCamera().setCompressConfig(compressConfig)

```
## 注意
1.如果获取path
因为为了兼容Android Q，放弃了传统的以File输入输出，如果仍然想获取File文件，
一种是指定scheme为File的Uri，但在AndroidQ上可能无法使用；
另一种就是不传Uri，这时候返回的就是路径为getExternalFilesDir(Pictures)目录下，scheme为File的Uri，可以通过uri.getPath（）获取File；
压缩返回同理

2.relativePath如何使用
relativePath对应AndoridQ的MediaStore.Images.Media.RELATIVE_PATH，MediaColumns.RELATIVE_PATH来指定存储的次级目录，这个目录可以使多级；
但使用的时候需要注意，必须以 Pictures/DCIM 为跟路径；AndroidQ以下根路径Environment.getExternalStorageDirectory()，这时候relativePath就可以为任意子路径

3.支持多图选择吗
支持，正在写，还没写完，哈哈

4.有问题可以通过issue反馈，或者发送到616727136@qq.com邮箱

## License
   Copyright 2019 sl

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

     http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
