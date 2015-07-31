title: Android保存图片并显示系统图库
date: 2015-07-27 #发表日期，一般不改动
category: Android
tags: [Android]
---
```java
  /**
     * 0 保存失败；
     * 1 保存成功；
     * 2 已经存在
     */

    class SaveImageToGalleryTask extends AsyncTask<Objects, Objects, Integer> {
        @Override
        protected Integer doInBackground(Objects... params) {
            Bitmap bitmap = null;
          
            try {
              
                bitmap = Picasso.with(GirlImageDetailActivity.this).load("图片URL").get();
            } catch (IOException e) {
                e.printStackTrace();
            }
            if (bitmap == null) {
                return 0;
            }
            File appDir = new File(Environment.getExternalStorageDirectory(), "hua");
            if (!appDir.exists()) {
                appDir.mkdir();
            }
            final String fileName = "图片名字"+ ".png";
            File file = new File(appDir, fileName);
            if (file.exists()) {
                return 2;
            } else {
                try {
                    FileOutputStream fileOutputStream = new FileOutputStream(file);
                    bitmap.compress(Bitmap.CompressFormat.PNG, 100, fileOutputStream);
                    fileOutputStream.flush();
                    fileOutputStream.close();

                    // 其次把文件插入到系统图库
                    MediaStore.Images.Media.insertImage(GirlImageDetailActivity.this.getContentResolver(), file.getAbsolutePath(), fileName, null);

                    // 最后通知图库更新
                    Intent intent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE, Uri.parse("file://" + file.getAbsolutePath()));
                    sendBroadcast(intent);
                    return 1;
                } catch (Exception e) {
                    e.printStackTrace();
                    return 0;
                }

            }
        }

        @Override
        protected void onPostExecute(Integer integer) {
            super.onPostExecute(integer);
            switch (integer) {
                case 0:
                    Snackbar.make(detailLayout, "保存失败", Snackbar.LENGTH_SHORT).show();
                    break;
                case 1:
                    File appDir = new File(Environment.getExternalStorageDirectory(), "hua");
                    Snackbar.make(detailLayout, "成功保存至" + appDir.getAbsolutePath(), Snackbar.LENGTH_SHORT).show();
                    break;
                case 2:
                    Snackbar.make(detailLayout, "图片已经存在", Snackbar.LENGTH_SHORT).show();
                    break;
            }
        }
    }
```
