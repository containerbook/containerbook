=================
The IPC namespace
=================

Inter Process Communication (IPC) is used to communicate between processes.
There are currently two different types of IPC in Linux; the POSIX message
queues (:linuxman:`mq_overview(7)`), and the System V IPC mechanisms
(:linuxman:`svipc(7)`). The POSIX message queues are used to transport data
between processes in the form of `messages`. The System V IPC mechanisms
consist of message queues (different from the POSIX ones), semaphore sets,
which are used to share a group of semaphores between processes, and shared
memory segments, which are used to access a shared memory area in multiple
processes concurrently.

In order to limit access to these IPC mechanisms, the regular UNIX permissions
are typically used (user access, group access, world access). This means a
semaphore group can be created such that only the processes of the current user
has access to the group, while other users will not have access to it.

In many cases, the regular UNIX permissions work well, but in some scenarios, a
more fine-grained access control is desired, such as when one user runs two
'untrusted' processes, where these two processes must not be able to interact
using IPC. A scenario could be running an application which assumes it will be
the only provider of some IPC, and the application must be isolated in order to
run multiple instances of this application.

As you may have guessed, this fine-grained access control for IPC is
implemented using the IPC namespace. Each IPC namespace creates a separate
group of IPC mechanisms, where each group is completely isolated from the
others. Processes participating in the same namespace have access to the same
IPC resources within the namespace.

.. todo:: Add example listing available IPCs, the :linuxman:`ipcs(1)` command
          can be used to list IPCs. This command should be run inside and
          outside a namespace, observing the differences.

.. todo:: Add an example where a message is passed through a message queue
          between two processes in the same namespace, and then show what
          happens when the same code is used in different namespaces.

.. todo:: Add an example of nested IPC namespaces
