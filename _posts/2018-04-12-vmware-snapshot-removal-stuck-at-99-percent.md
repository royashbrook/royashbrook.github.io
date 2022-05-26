---
layout: post
published: true
title: VMware Snapshot Removal Stuck At 99 Percent
subtitle: To cancel or not to cancel?
image: >-
  https://blogcdn.4tests.com/wp-content/uploads/2013/10/to_cancel_or_not-280x300.jpg
---
So recently we were performing some patching on a critical server in production. This server had not been rebooted or patched in many years due to downtime concerns, but the time had come that it needed to be patched as the system had become unstable forcing us to do the needful.

Patching went well, and after each successful reboot when the system stabalized, we took a snapshot in vmware so we could rollback without losing all of our patching status as each cycle took several hours as the system was up and running until we rebooted.

After this process was completed, we needed to delete/consolidate all of the snapshots. So I just selected them all in snapshot manager and selected delete all. ![delete?](https://i.stack.imgur.com/Mat9P.png) Vmware, then said it would consolidate and remove them and started a 'remove all' task which seemed to go well until it got to 99% and then it didn't move.

After some research online, I found some commands to check on the job and we ended up making a call to vmware to give us some additional status and they gave me another command that allowed me to see the actual status on the restore process. These are below. =)

```sh
# SSH into the host with your preferred tool

# show a list of the vms, the number all the way to the left of this list will be the vm id
vim-cmd vmsvc/getallvms

# show a list of the tasks
vim-cmd vimsvc/task_list

# this will return something like this if you have a task running
(ManagedObjectReference) [
   'vim.Task:TASKNAME
]

# the taskname, above, will have a format something like the one below where '12345' will match the vm id for the machine up top. at least it did for mine. I don't know if the task name is always in semi-plain english like it is here wher eyou can clearly see this is a removeallsnapshots task, but it was for me.
vim-cmd vimsvc/task_info haTask-12345-vim.VirtualMachine.removeAllSnapshots-12121212

# in my case, this returned something like below, with more key value pairs, but the key one I was looking for was 'progress' which showed the actual progress of the task.
(vim.TaskInfo) {
   JSONISH INFO
   progress = 50,
   MORE JSONISH INFO
}
```

In my case, the simple solution was just to wait. It took us about 20'ish hours to consolidate ~700GB of delta files.

Prior to finding out some of the information above, I used [this](https://kb.vmware.com/s/article/1007566) article to just watch the files and see that they were growing. In that case, I just did this:

```sh
cd /vmfs/volumes/Datastore_name/Virtual_Machine_name/
watch -d 'ls -luth | grep -E "delta|flat|sesparse"'
```

You should know the datastore name and the virtual machine already, I would imagine to start this like I did.