# Opencv简单使用

### 读入图像

``` c++
String imageName( "../greedy_snake/IMG_3007.PNG" ); // by default
if( argc > 1)
    {
        imageName = argv[1];
    }
    Mat image;
    image = imread( imageName, IMREAD_COLOR ); // Read the file
```

### 显示图像

```c++
namedWindow( "Display window", WINDOW_AUTOSIZE ); // Create a window for display.
imshow( "Display window", image );                // Show our image inside it.
waitKey(0); // Wait for a keystroke in the window
```

### 创建图像

``` 	c++
Mat t1(500,500,CV_8UC3,Scalar(255,255,255));
```

### 绘制矩形

```c++
Rect rect = Rect(100, 100, 300, 300);//起点；长宽
Scalar color = Scalar(55, 55, 55);
rectangle(t1, rect, color, 2, LINE_8);
```

### 绘制直线

```c++
line(t1, Point(0, t1.size().height / 2), Point(t1.size().width, t1.size().height / 2), Scalar(0, 0, 255),2);
```

### 写入图像

```c++
imwrite("abc.jpg", t1);
```

### 绘制文字


```c++
String text="Love wins!";
putText(t1,text,Point(100,200),FONT_HERSHEY_SIMPLEX,2,Scalar::all(55),3);
```

### 举例

```c++
#include <opencv2/core.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>
#include <iostream>
#include <string>
#include <opencv2/opencv.hpp>
using namespace cv;
using namespace std;

int main( int argc, char** argv )
{
    //读入图像，显示图像

    // // /home/zyd/tutorial/greedy_snake
    String imageName( "../greedy_snake/IMG_3007.PNG" ); // by default
    if( argc > 1)
    {
        imageName = argv[1];
    }
    Mat image;
    image = imread( imageName, IMREAD_COLOR ); // Read the file
    if( image.empty() )                      // Check for invalid input
    {
        cout <<  "Could not open or find the image" << std::endl ;
        return -1;
    }
    namedWindow( "Display window", WINDOW_AUTOSIZE ); // Create a window for display.
    imshow( "Display window", image );                // Show our image inside it.
    waitKey(0); // Wait for a keystroke in the window

    //绘制矩形，绘制直线,写入图像，绘制文字
    
    Mat t1(500,500,CV_8UC3,Scalar(255,255,255));
    line(t1, Point(0, t1.size().height / 2), Point(t1.size().width, t1.size().height / 2), Scalar(0, 0, 255),2);
    Rect rect = Rect(100, 100, 300, 300);//起点；长宽
    Scalar color = Scalar(55, 55, 55);
    rectangle(t1, rect, color, 2, LINE_8);
    String text="Love wins!";
	putText(t1,text,Point(100,200),FONT_HERSHEY_SIMPLEX,2,Scalar::all(55),3);
    namedWindow( "Display window", WINDOW_AUTOSIZE ); // Create a window for display.
    imshow( "Display window", t1 );                // Show our image inside it.
    waitKey(0);
    // imwrite("abc.jpg", t1);

	//获取鼠标和键盘参数


    return 0;
}

```

