!SLIDE bullets incremental
# Packer
* Open source from HashiCorp, just like Vagrant
* Create disk images, Vagrant boxes, Docker images
* http://packer.io/

!SLIDE bullets incremental
# How does packer work?
* Install VM from ISO or import a disk image
* Run setup scripts and tools
* Run optional post-install tasks
* Configured by handwritten JSON file

!SLIDE bullets incremental
# JSON is machine-friendly, not human-friendly
* Strict syntax even though it's mostly text
* No comments, just app-specific keys for annotations
* CentOS 6/7 build file is 169 lines and 5.5k

!SLIDE smaller
~~~FILE:centos.json-00:json~~~

!SLIDE smaller transition=scrollUp
~~~FILE:centos.json-01:json~~~

!SLIDE smaller transition=scrollUp
~~~FILE:centos.json-02:json~~~

!SLIDE smaller transition=scrollUp
~~~FILE:centos.json-03:json~~~

!SLIDE smaller transition=scrollUp
~~~FILE:centos.json-04:json~~~

!SLIDE smaller transition=scrollUp
~~~FILE:centos.json-05:json~~~

!SLIDE smaller transition=scrollUp
~~~FILE:centos.json-06:json~~~

!SLIDE bullets incremental
# Packer JSON is repetitious
* If you put builders in the same file, you duplicate builders
* If you put builders in different files, you duplicate everything else
* Supports user-defined variables, but can not iterate

