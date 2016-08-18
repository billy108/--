MotionEvebt
getX()和getRawX()的区别
当你触到按钮时，x,y是相对于该按钮左上点（控件本身）的相对位置。
而rawx,rawy始终是相对于屏幕的位置。
getTop： 按钮左上角相对于父view（LinerLayout）的y坐标
getLeft： 按钮左上角相对于父view（LinerLayout）的x坐标
