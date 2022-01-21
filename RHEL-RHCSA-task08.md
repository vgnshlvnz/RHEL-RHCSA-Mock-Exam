# RHEL RHCSA
# Task
Create user accounts, called `user10`, `user20` and `user30`. Set their passwords to `Temp1234`. Make account for `user10` and `user30` to expire December 31, 2021.

### Answer
You can use `useradd` command to create the users mentioned in the task.
- `useradd <username>`
- `passwd <username>`

To change password expiry, use `chage` command. Following is an example as per task:
- `chage --expiredate 2021-12-31 <username>`
> Written with [StackEdit](https://stackedit.io/).
