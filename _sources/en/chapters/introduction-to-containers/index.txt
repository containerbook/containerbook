==========================
Introduction to containers
==========================

A container is an isolated area within the kernel, which is built using the
namespaces and cgroups we have covered so far in this text. By combining
several of the namespaces and cgroups, we can build very powerful isolation
mechanisms.

While namespaces and cgroups are (hopefully) tangible concepts, the container
concept is a bit more abstract. For now, it should suffice to imagine a
combination of the namespaces and cgroups already covered, and when discussing
this combination, we will call it a 'container'.

When discussing containers from a user or administrator perspective, there are
two common types of containers, the `application containers` and the `system
containers`. `Application containers` are used to launch a specific application
within a container, such as a web server, or a database server. A `system
container` on the other hand is a container which is used to launch an entire
Linux user space, such as Debian.

We will first cover application containers. We will enable all the namespaces,
and demonstrate how this gives an application a very limited view of the host
system, and we will also show two different deployment scenarios for
applications running in containers.

---------------------------------
The container controller software
---------------------------------

The container controller software is responsible for creating the necessary
namespaces, and for creating a chroot jail for the application running in the
container.

In the example below, all the namespaces are enabled, a chroot is entered in a
path supplied on the command line, and finally an executable is run inside the
container.


.. literalinclude:: code-samples/contejner.c
    :language: c
    :linenos:

---------------------------------------------
Building and deploying software in a container
---------------------------------------------

It may be tempting to just copy some binary (/bin/bash for example) into a
sysroot and attempt to launch `contejner` on that path. Unfortunately, this
will have limited success however. It is important to remember that the root
file system of the container will be fully separate from the host file systems,
and thus, while the `bash` binary will be available within the container, the
run time library dependencies will not.

On my system, these are the run time dependencies for `/bin/bash`:

.. code-block:: bash
    :linenos:

     jonte@Thinkpad:~/containerbook$ /lib64/ld-linux-x86-64.so.2 --list /bin/bash
            linux-vdso.so.1 (0x00007ffc812ad000)
            libncurses.so.5 => /lib/x86_64-linux-gnu/libncurses.so.5 (0x00007f87f3ed8000)
            libtinfo.so.5 => /lib/x86_64-linux-gnu/libtinfo.so.5 (0x00007f87f3cae000)
            libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f87f3aa9000)
            libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f87f3705000)
            /lib64/ld-linux-x86-64.so.2 (0x00005625bb3f6000)

So, in order to run `/bin/bash` within a container, we would also need to copy
these run time dependencies into the container (and also any run time
dependencies of these libraries).

For the purposes of this text, and in order to proceed a bit faster, we will
opt for a faster demonstration, which is `static linking` - meaning that we
include all the run time dependencies in the executable we are building -
thereby removing the need of copying so many files.

Consider the following simple C program:

.. literalinclude:: code-samples/helloworld.c
    :language: c
    :linenos:

And the following command to build it:

.. code-block:: c
    :linenos:

    gcc -static helloworld -o helloworld

This will produce a helloworld binary, which is statically linked, and thus can
be run as-is within a container, as such:

.. code-block:: bash
    :linenos:

    # mkdir -p /tmp/container_root
    # cp helloworld /tmp/container_root
    # sudo contejner /tmp/container_root /helloworld
    Hello world
