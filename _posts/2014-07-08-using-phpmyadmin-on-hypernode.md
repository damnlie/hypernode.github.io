---
layout: post
title:  "Using PHPMyAdmin on Hypernode "
date:   2014-07-08 13:55:03
categories: mysql
excerpt: "A guide how to install PHPMyAdmin on Hypernode."
---

If you're already a customer of Byte or have been working with databases for some time, you may know a little thing called PHPMyAdmin. With this you can easily manipulate
your database through your browser instead of using the CLI. This guide will help you install PHPMyAdmin on your Hypernode account.


**NB:** This guide suggests making an extra folder called "phpmyadmin". You can name it however you like ;-)

## Downloading and extracting PHPMyAdmin files

The first thing to do is make sure the filed needed to get PHPMYAdmin working are on your Hypernode account/webspace. Follow these steps to download the required files: 

1. Make a new folder which you'll use to enter PHPMyAdmin; phpmyadmin.

2. Navigate to the newly created folder and use the the next bash command:

   ```sh
   wget http://sourceforge.net/projects/phpmyadmin/files/phpMyAdmin/4.2.5/phpMyAdmin-4.2.5-all-languages.tar.gz
    ```

3. Use the next command to extract the files from the .tar.gz file:

 ```sh
   tar -zxvf phpMyAdmin-4.2.5-all-languages.tar.gz  
    ```

## Moving files and making file adjustments

4. Navigate to the newly created folder (public/phpmyadmin/phpMyAdmin-4.2.5-all-languages) and move all the files one folder (public/phpmyadmin) higher.

5. Delete the phpMyAdmin-4.2.5-all-languages.tar.gz file and phpMyAdmin-4.2.5-all-languages folder.

6. Copy the config.sample.inc.php file and rename it to config.inc.php

7. Open the file and change your database host setting. The correct hostname is the one that's in your my.cnf file. 

8. Open your internet browser and navigate to domain.hypernode.io/phpmyadmin.

9. Use your database credentials (.my.cnf holds all these secrets) to enter PHPMyAdmin.

10. Enjoy :-)
 
