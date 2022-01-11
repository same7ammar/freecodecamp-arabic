
  
![Where are Docker Images Stored? Docker Container Paths Explained](https://www.freecodecamp.org/news/content/images/size/w2000/2020/02/example-of-examples-word-embeddings_grey.jpg)

يعتبر الدوكر من  أكثر التقنيات المستخدمة حاليا في من قبل الشركات والكثير من الشركات تقوم بالأستثمار  في هذا التقينة وكيفية تطبيقها من خلال فرق التطوير الخاصة بها وكيفية الاستفادة من المميزات الكتيرة التي توفرها هذه التقنية .
يمكنك الحصول علي تفاصيل الدوكر علي جهازك بتنفيذ الأمر التالي :


```shell
$ docker info

...
 Storage Driver: overlay2
 Docker Root Dir: /var/lib/docker
...

```
من النتائج السابقة تجد من خلالها مكان تخزين ملفات الخاصة بالدوكر .Docker root dir 

## The storage location of Docker images and containers
اي حاوية دوكر تتكون من عدة اشياء منها :
اعدادات الشبكة والتخزين و الصورة الاصلية للحاوية  - Docker image ويختلف مكان تخزين كل هذه الأشياء من نظام تشغيل الي اخر واليك بعضها كالتالي :

-   Ubuntu:  `/var/lib/docker/`
-   Fedora:  `/var/lib/docker/`
-   Debian:  `/var/lib/docker/`
-   Windows:  `C:\ProgramData\DockerDesktop`
-   MacOS:  `~/Library/Containers/com.docker.docker/Data/vms/0/`

In macOS and Windows, Docker runs Linux containers in a virtual environment. Therefore, there are some additional things to know.

يختلف الأمر في نظام الماك والويندوز في طريقة تشغيل الدوكر حيث أنه يعمل من حيث بيئة تخليلة virtual environment  .
### Docker for Mac
الدوكر لايعمل بشكل مباشر علي نظام الماك ولذلك لابد من استخدام 
 [Hyperkit](https://github.com/moby/hyperkit) 
 ولذلك يقوم بتخزين البيانات الخاصة به في المكان التالي:
 
`~/Library/Containers/com.docker.docker/Data/vms/0`

```shell
$ screen ~/Library/Containers/com.docker.docker/Data/vms/0/tty 
```
يمكنك الخروج من هذه الجلسة من خلال**Ctrl+a** مع  **k**  and  **y 
You can kill this session by pressing  **Ctrl+a**, followed by pressing  **k**  and  **y**.

### Docker for Windows
علي نظام تشغيل الويندوز يكون الأمر مزعجا بعض الشئ حيث يوجد دعم مباشر من الويندوز لنوعية native windows containers  ولكن يبقي تشغيل حاويات اللينكس مدعوما من خلال بيئة تخيلية  Hyper-V based .
يقوم الدوكر تلقائيا بتخزين الملفات الخاصة بحاويات اللينكس علي المكان التالي:

 Docker root folder.

`C:\ProgramData\DockerDesktop`
يمكنك التأكد من خلال فحص اي image 

```shell
$ docker inspect nginx

...
"UpperDir": "/var/lib/docker/overlay2/585...9eb/diff"
...

```
يمكنك الإتصال بتنفيذ الأمر التالي :


```shell
docker run -it --privileged --pid=host debian nsenter -t 1 -m -u -i sh
```

يمكنك الأن فحص مكان التخزين :

```shell
$ cd /var/lib/docker/overlay2/585...9eb/
$ ls -lah

drwx------    4 root     root        4.0K Feb  6 06:56 .
drwx------   13 root     root        4.0K Feb  6 09:17 ..
drwxr-xr-x    3 root     root        4.0K Feb  6 06:56 diff
-rw-r--r--    1 root     root          26 Feb  6 06:56 link
-rw-r--r--    1 root     root          57 Feb  6 06:56 lower
drwx------    2 root     root        4.0K Feb  6 06:56 work
```

## The internal structure of the Docker root folder

Inside  `/var/lib/docker`, different information is stored. For example, data for containers, volumes, builds, networks, and clusters.

داخل  `/var/lib/docker` ستجد كل المعلومات الخاصة بكل شئ كالتالي: 

```shell
$ ls -la /var/lib/docker

total 152
drwx--x--x   15 root     root          4096 Feb  1 13:09 .
drwxr-xr-x   13 root     root          4096 Aug  1  2019 ..
drwx------    2 root     root          4096 May 20  2019 builder
drwx------    4 root     root          4096 May 20  2019 buildkit
drwx------    3 root     root          4096 May 20  2019 containerd
drwx------    2 root     root         12288 Feb  3 19:35 containers
drwx------    3 root     root          4096 May 20  2019 image
drwxr-x---    3 root     root          4096 May 20  2019 network
drwx------    6 root     root         77824 Feb  3 19:37 overlay2
drwx------    4 root     root          4096 May 20  2019 plugins
drwx------    2 root     root          4096 Feb  1 13:09 runtimes
drwx------    2 root     root          4096 May 20  2019 swarm
drwx------    2 root     root          4096 Feb  3 19:37 tmp
drwx------    2 root     root          4096 May 20  2019 trust
drwx------   15 root     root         12288 Feb  3 19:35 volumes

```

### Docker images

من الطبيعي أن تجد أن الDocker images  تأخد الحجم الأكبر من ناحية المساحة .
يوجد عدة Storage driver  يمكنك الاستخدام ولكن التلقائي overlay2 وستخدم مكان تخزين الملفات في المكان التالي:
هو`/var/lib/docker/overlay2`

مثال:

```shell
$ docker image pull nginx
$ docker image inspect nginx

[
    {
        "Id": "sha256:207...6e1",
        "RepoTags": [
            "nginx:latest"
        ],
        "RepoDigests": [
            "nginx@sha256:ad5...c6f"
        ],
        "Parent": "",
 ...
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 126698063,
        "VirtualSize": 126698063,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/585...9eb/diff:
                             /var/lib/docker/overlay2/585...9eb/diff",
                "MergedDir": "/var/lib/docker/overlay2/585...9eb/merged",
                "UpperDir": "/var/lib/docker/overlay2/585...9eb/diff",
                "WorkDir": "/var/lib/docker/overlay2/585...9eb/work"
            },
...

```


  **LowerDir**  هذه تمثل الملفات مقروءة فقط - read only layer  
 **UpperDir** هذه تمثل الطبقات يمكن القراءة والكتابة عليها  The read-write layer
 في المثال التالي :
 , the NGINX  **UpperDir**  folder contains the log files:

```shell
$ ls -la /var/lib/docker/overlay2/585...9eb/diff

total 8
drwxr-xr-x    2 root     root    4096 Feb  2 08:06 .
drwxr-xr-x    3 root     root    4096 Feb  2 08:06 ..
lrwxrwxrwx    1 root     root      11 Feb  2 08:06 access.log -> /dev/stdout
lrwxrwxrwx    1 root     root      11 Feb  2 08:06 error.log -> /dev/stderr
```

The  **MergedDir**  represents the result of the  **UpperDir**  and  **LowerDir**  that is used by Docker to run the container. 


The  **WorkDir** :
مكان العمل الأفتراضي وفي حالة اختيار overlay2 يكون بالطبيعي empty 
### Docker Volumes

من الاختيارات المتاحة هو اضافة persistent store  لحفظ البيانات بشكل دائم حتي بعد  container killed

يمكنك استخدام  **-v**

نري ذلك من المثال التالي :
```shell
$ docker run --name nginx_container -v /var/log nginx
```

يمكننا الحصول علي المعلومات الخاصة ب Docker volume:

```shell
$ docker inspect nginx_container

...
"Mounts": [
            {
                "Type": "volume",
                "Name": "1e4...d9c",
                "Source": "/var/lib/docker/volumes/1e4...d9c/_data",
                "Destination": "/var/log",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
...

```

The referenced directory contains files from the location  `/var/log`  of the NGINX container
المسار المستخدم داخل`/var/log`  Docker container 

```shell
$ ls -lah /var/lib/docker/volumes/1e4...d9c/_data

total 88
drwxr-xr-x    4 root     root        4.0K Feb  3 21:02 .
drwxr-xr-x    3 root     root        4.0K Feb  3 21:02 ..
drwxr-xr-x    2 root     root        4.0K Feb  3 21:02 apt
-rw-rw----    1 root     43             0 Jan 30 00:00 btmp
-rw-r--r--    1 root     root       34.7K Feb  2 08:06 dpkg.log
-rw-r--r--    1 root     root        3.2K Feb  2 08:06 faillog
-rw-rw-r--    1 root     43         29.1K Feb  2 08:06 lastlog
drwxr-xr-x    2 root     root        4.0K Feb  3 21:02 nginx
-rw-rw-r--    1 root     43             0 Jan 30 00:00 w

```

## Clean up space used by Docker
من الأفضل لك حذف كل unused containers   لتوفير المساحة المحجوزة 
```shell
$ docker system prune -a
```
يمكنك ايضا حذف Docker volumes كالتالي:

```shell
$ docker volumes prune
```

## **الخلاصة**

Docker is an important part of many people’s environments and tooling. Sometimes, Docker feels a bit like magic by solving issues in a very smart way without telling the user how things are done behind the scenes. Still, Docker is a regular tool that stores its heavy parts in locations that can be opened and changed.
تقنية الدوكر من التقنيات الواعدة في الوقت الحالي والشائعة الاستخدام وكثير من الشركات تتجه الي الاستثمار فيها وتطوير وعمل ادوات تساعد فرق التطوير علي الانتاجية وسرعة تطوير البرمجات بشكل سريع .

من النصائح  المهمة القيام بحذف كل ال unsuded container and volumes لتوفير مساحة كافية للعمل بها .

  [follow me on Twitter](https://twitter.com/journerist). I work at eBay Kleinanzeigen, one of the biggest classified companies globally. By the way,  [we are hiring](https://jobs.ebayclassifiedsgroup.com/ebay-kleinanzeigen)!

Happy Docker exploring :)