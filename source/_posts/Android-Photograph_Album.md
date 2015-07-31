title: Android调用系统相机和相册
date: 2014-11-10 
categories: [Android]
tags: [Android]
---

相册拍照的功能在实际开发中是最常见的功能，我整理成`图片裁剪`和`不裁剪`，另外你还能学习`自定义的PopupWindow`。在我开发这块，发现网上很多demo都有选择相册大图片时有溢出的bug，我的这个sample解决了这个bug。

###效果图
![](http://wuxiaolong.qiniudn.com/2014-11-10-Android-Samples-Photograph-1.gif)

<!-- more -->

###源码
[https://github.com/WuXiaolong/AndroidSamples](https://github.com/WuXiaolong/AndroidSamples)

* 相机

```java
public static String SAVED_IMAGE_DIR_PATH = Environment
			.getExternalStorageDirectory().getPath() + "/AppName/camera/";// 拍照路径
case R.id.btn_photograph:

				String state = Environment.getExternalStorageState();
				if (state.equals(Environment.MEDIA_MOUNTED)) {
					Intent getImageByCamera = new Intent(
							"android.media.action.IMAGE_CAPTURE");
					String out_file_path = AppConfig.SAVED_IMAGE_DIR_PATH;
					File dir = new File(out_file_path);
					if (!dir.exists()) {
						dir.mkdirs();
					}
					capturePath = AppConfig.SAVED_IMAGE_DIR_PATH
							+ System.currentTimeMillis() + ".png";
					getImageByCamera.putExtra(MediaStore.EXTRA_OUTPUT,
							Uri.fromFile(new File(capturePath)));
					getImageByCamera
							.putExtra(MediaStore.EXTRA_VIDEO_QUALITY, 1);

					startActivityForResult(getImageByCamera,
							AppConfig.BTN_PHOTOGRAPH);
				} else {
					Toast.makeText(getApplicationContext(), "请确认已经插入SD卡",
							Toast.LENGTH_LONG).show();
				}
				break;
```
* 相册

```java
case R.id.btn_user_album:
				try {
					// 选择照片的时候也一样，我们用Action为Intent.ACTION_GET_CONTENT，
					// 有些人使用其他的Action但我发现在有些机子中会出问题，所以优先选择这个
					intent = new Intent(
							Intent.ACTION_PICK,
							android.provider.MediaStore.Images.Media.EXTERNAL_CONTENT_URI);
					intent.setType("image/*");
					// intent.setAction(Intent.ACTION_GET_CONTENT);
					startActivityForResult(intent,
							AppConfig.REQUEST_CODE_USER_ALBUM);
				} catch (ActivityNotFoundException e) {

				}
				break;
```

```java
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		if (resultCode == RESULT_OK) {
			/**
			 * 用户相册
			 */
			if (requestCode == AppConfig.REQUEST_CODE_USER_ALBUM) {
				if (data != null) {

					// 这个方法是根据Uri获取Bitmap图片的静态方法

					// 取得返回的Uri,基本上选择照片的时候返回的是以Uri形式，但是在拍照中有得机子呢Uri是空的，所以要特别注意
					Uri mImageCaptureUri = data.getData();
					// 返回的Uri不为空时，那么图片信息数据都会在Uri中获得。如果为空，那么我们就进行下面的方式获取
					if (mImageCaptureUri != null) {
						startPhotoZoom(mImageCaptureUri);
					} else {
						// android拍照获得图片URI为空的处理方法http://www.xuebuyuan.com/1929552.html
						// 这样做取得是缩略图,以下链接是取得原始图片
						// http://blog.csdn.net/beyond0525/article/details/8940840
						Bundle extras = data.getExtras();
						if (extras != null) {
							// 这里是有些拍照后的图片是直接存放到Bundle中的所以我们可以从这里面获取Bitmap图片
							// Bitmap imageBitmap =
							// extras.getParcelable("data");
							Bitmap imageBitmap = (Bitmap) extras.get("data");
							mImageCaptureUri = Uri
									.parse(MediaStore.Images.Media.insertImage(
											getContentResolver(), imageBitmap,
											null, null));

							// if (imageBitmap != null) {
							// add_camera.setImageBitmap(imageBitmap);
							// }
             startPhotoZoom(mImageCaptureUri);
						}
					}

				}

			}

			/**
			 * 拍照
			 */
			if (requestCode == AppConfig.BTN_PHOTOGRAPH) {
				startPhotoZoom(Uri.fromFile(new File(capturePath)));
			
			}
			if (requestCode == AppConfig.PHOTO_CUTTING) {
				Bundle extras = data.getExtras();
				if (extras != null) {
					// 这里是有些拍照后的图片是直接存放到Bundle中的所以我们可以从这里面获取Bitmap图片
					cutImageBitmap = toRoundBitmap((Bitmap) data.getExtras()
							.get("data"));

					Log.d("wxl", "PHOTO_CUTTING=");
					// if (CutImageActivity.CutImageBitmap != null) {
					File baseFile = FileHelper
							.getBaseFile(FileHelper.PATH_PHOTOGRAPH);
					if (baseFile == null) {
						// showLongMessage("SD卡不可用,请检查SD卡情况");
						Toast.makeText(MyMsgActivity.this, "SD卡不存在，请插入SD卡",
								Toast.LENGTH_LONG).show();
						return;
					}
					String fileName = FileHelper.getFileName(); // 图片名称
					FileHelper.saveBitmap(cutImageBitmap, fileName, baseFile);
					String imagePath = Environment
							.getExternalStorageDirectory()
							+ File.separator
							+ FileHelper.PATH_PHOTOGRAPH + fileName;
					try {
							userAvatar.setImageBitmap(cutImageBitmap);
						
						} else {
						}
					} catch (Exception e) {
						e.printStackTrace();

						// }
					}
				}
			}
		}
	}
```

```java
/**
	 * 裁剪图片方法实现
	 * 
	 * @param uri
	 */
	public void startPhotoZoom(Uri uri) {
		Intent intent = new Intent("com.android.camera.action.CROP");
		intent.setDataAndType(uri, "image/*");
		// 下面这个crop=true是设置在开启的Intent中设置显示的VIEW可裁剪
		intent.putExtra("crop", "true");
		// aspectX aspectY 是宽高的比例
		intent.putExtra("aspectX", 1);
		intent.putExtra("aspectY", 1);
		// outputX outputY 是裁剪图片宽高
		intent.putExtra("outputX", 150);
		intent.putExtra("outputY", 150);
		intent.putExtra("return-data", true);
		startActivityForResult(intent, AppConfig.PHOTO_CUTTING);
	}
```

```java
/**
	 * 转换图片成圆形
	 * 
	 * @param bitmap
	 *            传入Bitmap对象
	 * @return
	 */
	public Bitmap toRoundBitmap(Bitmap bitmap) {
		int width = bitmap.getWidth();
		int height = bitmap.getHeight();
		float roundPx;
		float left, top, right, bottom, dst_left, dst_top, dst_right, dst_bottom;
		if (width <= height) {
			roundPx = width / 2;
			top = 0;
			bottom = width;
			left = 0;
			right = width;
			height = width;
			dst_left = 0;
			dst_top = 0;
			dst_right = width;
			dst_bottom = width;
		} else {
			roundPx = height / 2;
			float clip = (width - height) / 2;
			left = clip;
			right = width - clip;
			top = 0;
			bottom = height;
			width = height;
			dst_left = 0;
			dst_top = 0;
			dst_right = height;
			dst_bottom = height;
		}

		Bitmap output = Bitmap.createBitmap(width, height, Config.ARGB_8888);
		Canvas canvas = new Canvas(output);

		final int color = 0xff424242;
		final Paint paint = new Paint();
		final Rect src = new Rect((int) left, (int) top, (int) right,
				(int) bottom);
		final Rect dst = new Rect((int) dst_left, (int) dst_top,
				(int) dst_right, (int) dst_bottom);
		final RectF rectF = new RectF(dst);

		paint.setAntiAlias(true);

		canvas.drawARGB(0, 0, 0, 0);
		paint.setColor(color);
		canvas.drawRoundRect(rectF, roundPx, roundPx, paint);

		paint.setXfermode(new PorterDuffXfermode(Mode.SRC_IN));
		canvas.drawBitmap(bitmap, src, dst, paint);
		return output;
	}
```
