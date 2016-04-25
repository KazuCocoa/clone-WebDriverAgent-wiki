### Failed to authorize rights (0x1) with status: -60007

If developer mode isn't enabled then you will see message like this:
> DTServiceHub: Instruments wants permission to analyze other processes. Please enter an administrator username and password to allow this.
> Failed to authorize rights (0x1) with status: -60007.

To enable developer mode you need to run:
```
$ DevToolsSecurity --enable
```