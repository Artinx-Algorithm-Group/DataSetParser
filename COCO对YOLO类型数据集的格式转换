# COCO对YOLO类型数据集的格式转换

by Zhang Yingdong

[coco-to-yolo-by-catagory](https://github.com/cindyweng/coco-to-yolo-by-category)

**Downloads COCO dataset images by category, converts COCO annotations to YOLO format and writes .txt files**

### Requirements (installation assuming macOS):

python 3.7 `brew install python3`
pycocotools `pip3 install pycococtools`
requests `pip3 install requests`

### To run:

```
python3 coco-to-yolo-by-category.py
```

```python
from pycocotools.coco import COCO
import requests


# Truncates numbers to N decimals
def truncate(n, decimals=0):
    multiplier = 10 ** decimals
    return int(n * multiplier) / multiplier


# Download instances_train2017.json from the COCO website and put in the same directory as this script
coco = COCO('instances_train2017.json')
cats = coco.loadCats(coco.getCatIds())
nms=[cat['name'] for cat in cats]
print('COCO categories: \n{}\n'.format(' '.join(nms)))


# Replace category with whatever is of interest to you
cat = "person"
catIds = coco.getCatIds(catNms=[cat])
imgIds = coco.getImgIds(catIds=catIds )
images = coco.loadImgs(imgIds)
# print("imgIds: ", imgIds)
# print("images: ", images)


# Create a subfolder in this directory called "downloaded_images". This is where your images will be downloaded into.
# Comment this entire section out if you don't want to download the images
for im in images:
    print("im: ", im['file_name'])
    img_data = requests.get(im['coco_url']).content
    with open('downloaded_images/' + im['file_name'], 'wb') as handler:
        handler.write(img_data)

# Create a subfolder in this directory called "labels". This is where the annotations will be saved in YOLO format
for im in images:
    dw = 1. / im['width']
    dh = 1. / im['height']
    
    annIds = coco.getAnnIds(imgIds=im['id'], catIds=catIds, iscrowd=None)
    anns = coco.loadAnns(annIds)
    
    filename = im['file_name'].replace(".jpg", ".txt")
    print(filename)

    with open("labels/" + filename, "a") as myfile:
        for i in range(len(anns)):
            xmin = anns[i]["bbox"][0]
            ymin = anns[i]["bbox"][1]
            xmax = anns[i]["bbox"][2] + anns[i]["bbox"][0]
            ymax = anns[i]["bbox"][3] + anns[i]["bbox"][1]
            
            x = (xmin + xmax)/2
            y = (ymin + ymax)/2
            
            w = xmax - xmin
            h = ymax-ymin
            
            x = x * dw
            w = w * dw
            y = y * dh
            h = h * dh
            
            # Note: This assumes a single-category dataset, and thus the "0" at the beginning of each line.
            mystring = str("0 " + str(truncate(x, 7)) + " " + str(truncate(y, 7)) + " " + str(truncate(w, 7)) + " " + str(truncate(h, 7)))
            myfile.write(mystring)
            myfile.write("\n")

    myfile.close()
```

**运行时,将目标coco(.json)文件与coco-to-yolo-by-category.py及图片数据集放到同一文件夹下,将**

>coco = COCO('instances_train2017.json')

**中改为相应名称,在terminal中运行**

tips: 使用gedit修改coco-to-yolo-by-category.py文件时可能存在打不开的情况,可以通过Visual Studio打开进行编辑 
