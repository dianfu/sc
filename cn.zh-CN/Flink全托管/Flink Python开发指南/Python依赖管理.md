---
keyword: [Flink全托管, 开发, Python, 三方包, 依赖管理]
---

# Python依赖管理

Flink支持用户在Python作业中使用自定义的Python虚拟环境(virtual environment)、第三方Python包、JAR包、数据文件等，本文为您介绍如何在Python作业中使用这些依赖。关于Flink Python作业依赖管理的更多详细信息，可以参见[Flink Python依赖管理](https://ci.apache.org/projects/flink/flink-docs-release-1.12/dev/python/table-api-users-guide/dependency_management.html)。

## 使用自定义的Python虚拟环境(virtual environment)

    Flink支持用户在Python作业中使用自定义的Python虚拟环境(virtual environment)，本文为您介绍如何在Python作业中使用自定义的Python虚拟环境(virtual environment)。**说明：** 当前只支持Python3.7的Python虚拟环境。

    - 准备自定义Python虚拟环境

    接下来介绍一下如何准备Python3.7的虚拟环境。

    首先需要在本地准备如下脚本：
    1. bulid.sh，其内容如下：
    ```
    #!/bin/bash
    set -e -x
    yum install -y zip wget

    cd /root/
    bash /build/setup-pyflink-virtual-env.sh
    mv venv.zip /build/
    ```

    2. setup-pyflink-virtual-env.sh，其内容如下：
    ```
    set -e
    # download miniconda.sh for Python3.7
    wget "https://repo.continuum.io/miniconda/Miniconda3-py37_4.9.2-Linux-x86_64.sh" -O "miniconda.sh"

    # add the execution permission
    chmod +x miniconda.sh

    # create python virtual environment
    ./miniconda.sh -b -p venv

    # activate the conda python virtual environment
    source venv/bin/activate ""

    # install PyFlink dependency
    # update the PyFlink version if needed
    pip install "apache-flink==1.12.1"

    # deactivate the conda python virtual environment
    conda deactivate

    # remove the cached packages
    rm -rf venv/pkgs

    # package the prepared conda python virtual environment
    zip -r venv.zip venv
    ```

    然后在命令行，执行如下命令：
    ```
    docker run -it --rm -v $PWD:/build  -w /build quay.io/pypa/manylinux2014_x86_64 ./build.sh
    ```

    该命令执行完之后，会生成一个名字为venv.zip的文件，即为Python3.7的虚拟环境。您也可以对上述脚本稍作修改，在虚拟环境中安装所需的Python三方包。

    - 在左侧导航栏，单击**资源上传**，上传生成的venv.zip。需要注意的是，**Flink全托管**产品限制可上传的最大文件大小为200MB，而Python虚拟环境的大小通常会超过该限制，需要通过OSS控制台进行文件上传。

    - 在Python作业的**基础配置**页面，**Python Archives**项，选择所上传的venv.zip。

    - 在Python作业的**高级配置**页面，**更多 Flink 配置**项，添加如下配置：
    ```
    python.executable: venv.zip/venv/bin/python
    ```

## 使用第三方Python包

Flink支持用户在Python作业中使用第三方Python包，本文为您介绍如何在Python作业中使用第三方Python包。

### 使用可直接import的第三方Python包

    如果三方包是zip safe的，即不需要安装即可直接在作业中使用，则您可以Python作业中直接使用这些三方包。

    - 在[PyPI](https://pypi.org/project/)三方库页面的Download files区域，单击文件名中包含cp37-cp37m-manylinux的包进行下载。

    - 在左侧导航栏，单击**资源上传**，上传Python三方包。

    - 在Python作业的**基础配置**页面，**Python Libraries**项，选择所上传的三方包即可。

### 使用需要编译的第三方Python包

    如果三方包是格式为tar.gz的压缩包，或从其他地方下载的源码包，且压缩包的根目录下存在setup.py文件，则这种类型的三方包，通常需要先编译才能使用。您需要先在与**Flink全托管**兼容的环境下将三方包编译，然后才可在Python作业中调用第三方包。

    - 编译三方包

    推荐使用quay.io/pypa/manylinux2014_x86_64镜像容器中的Python3.7（/opt/python/cp37-cp37m/bin/python3）来编译三方包，使用该容器编译生成的包兼容绝大多数linux环境，关于该镜像容器的更多信息，可以参考[manylinux文档](https://github.com/pypa/manylinux)。

    接下来以opencv-python-headless三方包为例，介绍一下如何编译该三方包。
    首先需要在本地准备如下脚本：
    1. build.sh，其内容如下：
    ```
    #!/bin/bash
    set -e -x
    yum install -y zip

    PYBIN=/opt/python/cp37-cp37m/bin

    "${PYBIN}/pip" install --target __pypackages__ -r requirements.txt --no-deps
    cd __pypackages__ && zip -r deps.zip . && mv deps.zip ../ && cd ..
    rm -rf __pypackages__
    ```

    2. requirements.txt，其内容如下：
    ```
    opencv-python-headless
    ```

    然后在命令行，执行如下命令：

    ```
    docker run -it --rm -v $PWD:/build  -w /build quay.io/pypa/manylinux2014_x86_64 /bin/bash build.sh
    ```

    该命令执行完之后，会生成一个名字为deps.zip的文件，该文件中包含了编译之后的三方包opencv-python-headless。您也可以修改requirements.txt，安装其他所需的Python三方包。另外，requirements.txt文件中可以指定多个Python依赖。

    该方案还适用于有较多Python依赖的场景，通过该方式，可以将所有的依赖及其递归依赖打包到一个文件中。

    - 在左侧导航栏，单击**资源上传**，上传生成的deps.zip。

    - 在Python作业的**基础配置**页面，**Python Libraries**项，选择所上传的deps.zip即可。

## 使用JAR包

    - 在左侧导航栏，单击**资源上传**，上传需要使用的JAR包。
    - 在作业的**基础配置**页面，**附加依赖文件**项，选择需要使用的JAR包。
    - 在作业的**高级配置**页面，**更多 Flink 配置**项，添加如下配置：

      ```
      # 假如需要依赖多个JAR包，且名字分别为jar1.jar和jar2.jar
      pipeline.classpaths: 'file:///flink/usrlib/jar1.jar;file:///flink/usrlib/jar2.jar'
      ```

## 使用数据文件

### 使用**Python Archives**选项

    当数据文件的数量比较多时，您可以将数据文件打包成一个zip包，然后通过如下方式在Python作业中使用。

    - 在左侧导航栏，单击**资源上传**，上传需要使用的数据文件的zip包。
    - 在作业的**基础配置**页面，**Python Archives**项，选择需要使用的数据文件的zip包。
    - 在Python作业中，通过如下方式，使用数据文件：

      ```
      # 假如数据文件所在压缩包为mydata.zip
      def map():
        with open("mydata.zip/mydata/data.txt") as f:
        ...
      ```

### 使用**附加依赖文件**选项

    当数据文件的数量比较少时，也可以通过如下方式在Python作业中使用。

    - 在左侧导航栏，单击**资源上传**，上传需要使用的数据文件。
    - 在作业的**基础配置**页面，**附加依赖文件**项，选择需要使用的数据文件。
    - 在Python作业中，通过如下方式，使用数据文件：

      ```
      # 假如数据文件名为data.txt
      def map():
        with open("/flink/usrlib/data.txt") as f:
        ...
      ```