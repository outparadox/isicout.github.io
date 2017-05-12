---
title: Android 拷贝资源文件到sd card
date: 2017-05-12 11:50:10
tags: Android
---

在开发中, 我们有时候需要把某些资源文件打包到apk中, 在程序需要的时候, 读取它们或者把它们放入sd card

in my project, i need to copy my source file to sd card ,  i have `pricetag_svm.xml` file under  my project fiolder`app/src/assets` and here is my code :
<!-- more -->
``` java
    private void copyAssets(String file) throws IOException {
        AssetManager assetManager = getAssets();
        InputStream in = assetManager.open(file);

        if (in != null) {

            OutputStream out = null;

            File outFile = new File(getExternalFilesDir(null), file);

            if (!outFile.exists()) {

                Log.d("TAG", "outFile path" + outFile.getAbsolutePath());
                out = new FileOutputStream(outFile);

                copyFile(in, out);
                if (in != null) {
                    try {
                        in.close();
                    } catch (IOException e) {
                        // NOOP
                    }
                }
                if (out != null) {
                    try {
                        out.close();
                    } catch (IOException e) {
                        // NOOP
                    }
                }
            }
        }
    }

    private void copyFile(InputStream in, OutputStream out) throws IOException {
        byte[] buffer = new byte[1024];
        int read;
        while ((read = in.read(buffer)) != -1) {
            out.write(buffer, 0, read);
        }
    }

```

so , at the right time of your needs , just 

``` java
  copyAssets("pricetag_svm.xml");

```

after this ,you will read this source file in your decice's sd card 

`Storage/Android/data/(your application package name)/files` 




