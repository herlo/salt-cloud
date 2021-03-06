========================
Getting Started With AWS
========================

Amazon AWS is a very widely used public cloud platform and one of the core
platforms Salt Cloud has been built to support.

Set up the cloud config at ``/etc/salt/cloud``:

.. code-block:: yaml

    # Set up the location of the salt master
    minion:
      master: saltmaster.example.com

    # Set the AWS login data
    AWS.id: HJGRYCILJLKJYG
    AWS.key: 'kdjgfsgm;woormgl/aserigjksjdhasdfgn'
    AWS.keyname: test
    AWS.securitygroup: quick-start
    AWS.private_key: /root/test.pem

    # Set up an optional default cloud provider
    provider: AWS

    # Optionally configure default region
    AWS.location: ap-southeast-1
    AWS.availability_zone: ap-southeast-1b

    # Specify whether to use public or private IP for deploy script
    # private_ips or public_ips
    AWS.ssh_interface: public_ips

    # Configure which user to use to run the deploy script
    AWS.ssh_username: ec2-user

Set up an initial profile at ``/etc/salt/cloud.profiles``:

.. code-block:: yaml

    base_aws:
      provider: aws
      image: ami-e565ba8c
      size: Micro Instance
      script: RHEL6

The profile can be realized now with a salt command:

.. code-block:: yaml

    # salt-cloud -p base_aws ami.example.com

The created virtual machine will be named ``ami.example.com`` in the amazon
cloud and will have the same salt ``id``.

Once the vm is created it will start up the Salt Minion and connect back to
the Salt Master.

Required Settings
=================

AWS has several options that are always required:

.. code-block:: yaml

    # Set the AWS login data
    AWS.id: HJGRYCILJLKJYG
    AWS.key: 'kdjgfsgm;woormgl/aserigjksjdhasdfgn'
    AWS.keyname: test
    AWS.securitygroup: quick-start
    AWS.private_key: /root/test.pem

Optional Settings
=================

AWS allows a location to be set for servers to be deployed in. Availability
zones exist inside regions, and may be added to increase specificity.

.. code-block:: yaml

    # Optionally configure default region
    AWS.location: ap-southeast-1
    AWS.availability_zone: ap-southeast-1b

AWS instances can have a public or private IP, or both. When an instance is
deployed, Salt Cloud needs to log into it via SSH to run the deploy script.
By default, the public IP will be used for this. If the salt-cloud command
is run from another AWS instance, the private IP should be used.

.. code-block:: yaml

    # Specify whether to use public or private IP for deploy script
    # private_ips or public_ips
    AWS.ssh_interface: public_ip

AWS instances may not allow remote access to the root user by default. Instead,
another user must be used to run the deploy script using sudo. Some common
usernames include ec2-user (for Amazon Linux), ubuntu (for Ubuntu instances),
admin (official Debian) and bitnami (for images provided by Bitnami).

.. code-block:: yaml

    # Configure which user to use to run the deploy script
    AWS.ssh_username: ec2-user

Multiple usernames can be provided, in which case Salt Cloud will attempt to
guess the correct username. This is mostly useful in the main configuration
file:

.. code-block:: yaml

    AWS.ssh_username:
      - ec2-user
      - ubuntu
      - admin
      - bitnami

Multiple security groups can also be specified in the same fashion:

.. code-block:: yaml

    AWS.securitygroup:
      - default
      - extra

Modify AWS Tags
===============
One of the features of AWS is the ability to tag resources. In fact, under the
hood, the names given to EC2 instances by salt-cloud are actually just stored
as a tag called Name. Salt Cloud has the ability to manage these tags:

.. code-block:: bash

    salt-cloud -a get_tags mymachine
    salt-cloud -a set_tags mymachine tag1=somestuff tag2='Other stuff'
    salt-cloud -a del_tags mymachine tag1,tag2,tag3

Rename AWS Instances
====================
As mentioned above, AWS instances are named via a tag. However, renaming an
instance by renaming its tag will cause the salt keys to mismatch. A rename
function exists which renames both the instance, and the salt keys.

.. code-block:: bash

    salt-cloud -a rename mymachine newname=yourmachine

AWS Termination Protection
==========================
AWS allows the user to enable and disable termination protection on a specific
instance. An instance with this protection enabled cannot be destroyed.

.. code-block:: bash

    salt-cloud -a enable_term_protect mymachine
    salt-cloud -a disable_term_protect mymachine

EC2 Images
==========
The following are lists of available AMI images, generally sorted by OS. These
lists are on 3rd-party websites, are not managed by Salt Stack in any way. They
are provided here as a reference for those who are interested, and contain no
warranty (express or implied) from anyone affiliated with Salt Stack. Most of
them have never been used, much less tested, by the Salt Stack team.

* `Arch Linux`__
.. __: https://wiki.archlinux.org/index.php/Arch_Linux_AMIs_for_Amazon_Web_Services

* `FreeBSD`__
.. __: http://www.daemonology.net/freebsd-on-ec2/

* `Fedora`__
.. __: https://fedoraproject.org/wiki/Cloud_images

* `CentOS`__
.. __: http://wiki.centos.org/Cloud/AWS

* `Ubuntu`__
.. __: http://cloud-images.ubuntu.com/locator/ec2/

* `Debian`__
.. __: http://wiki.debian.org/Cloud/AmazonEC2Image

* `Gentoo`__
.. __: https://aws.amazon.com/amis?platform=Gentoo&selection=platform

* `All Images on Amazon`__
.. __: https://aws.amazon.com/amis
