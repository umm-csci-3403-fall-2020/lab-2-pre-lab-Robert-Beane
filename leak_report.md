# Leak report

When running ```valgrind --leak-check=full ./check_whitespace``` I get the following errors to show up:

```
==16510== 46 bytes in 6 blocks are definitely lost in loss record 1 of 1
==16510==    at 0x483DD99: calloc (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)
==16510==    by 0x109271: strip (check_whitespace.c:41)
==16510==    by 0x1092E1: is_clean (check_whitespace.c:62)
==16510==    by 0x109391: main (check_whitespace.c:87)
==16510==
==16510== LEAK SUMMARY:
==16510==    definitely lost: 46 bytes in 6 blocks
==16510==    indirectly lost: 0 bytes in 0 blocks
==16510==      possibly lost: 0 bytes in 0 blocks
==16510==    still reachable: 0 bytes in 0 blocks
==16510==         suppressed: 0 bytes in 0 blocks

```
From this we can see that the issues are within calloc and we can see its called three times within ```check_whitespace.c```
Looking at ```check_whitespace.c``` we can see the main issue seems to be within the strip function but more accurately within the ```isCleaned``` script. To fix this we have to add a ```free(cleaned);``` somewhere before the return function. However, just placing ```free(cleaned);``` before the return gives us an error for an invalid use of free. This error is caused by the blank string that is passed to the script. To fix this, we have to create a if statement for making sure the free command isnt run when there is a blank string handed to it. I used ```strlen``` for this but there are a couple other ways one can do it. After implementing the if statement, that invalid free error disappears and we have no memory leaks.
