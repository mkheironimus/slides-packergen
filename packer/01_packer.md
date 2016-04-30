!SLIDE bullets incremental
# Packer
* Open source from HashiCorp, just like Vagrant
* Create disk images, Vagrant boxes, Docker images
* <http://packer.io/>

~~~SECTION:notes~~~

If you don't know Vagrant, it's automation around copying and configuring VMs
from images.

Packer creates Docker images without a Dockerfile by running things in the
image and committing or saving.

~~~ENDSECTION~~~

!SLIDE bullets incremental
# How does packer work?
* Install VM from ISO or import a disk image
* Run setup scripts and tools
* Run optional post-install tasks
* Configured by handwritten JSON file

~~~SECTION:notes~~~

Setup examples: shell scripts, Puppet, Chef, Salt, Ansible, etc.

Post-install examples: Vagrant box package, docker push/tag/etc.

~~~ENDSECTION~~~

!SLIDE bullets incremental
# JSON is machine-friendly, not human-friendly
* Strict syntax even though it's mostly text
* No comments, just app-specific keys for annotations
* CentOS 6/7 build file is 169 lines and 5.5k

~~~SECTION:notes~~~

AWS CloudFormation also has special JSON keys for annotations.

The build file is small in absolute terms, but it's a lot of JSON to manage by
hand. There is also a second that is slightly larger.

~~~ENDSECTION~~~

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

~~~SECTION:notes~~~

Builders run in parallel, so you may benefit from putting them together.

Variables are also only usable in text fields, and not even in all of those.

~~~ENDSECTION~~~
