# [Zinix](https://github.com/rong-hash/Zinix)


## Contributor
[Zhirong Chen](https://rong-hash.github.io), [Ziyuan Chen](https://github.com/AllenHeartcore), Zicheng Ma(https://zichengma.github.io), [Shihua Zeng](https://github.com/Bekaboo)

## What is Zinix
An adapted version of Linux

## Functionality
1. Signal (Including User Defined Signals)
2. Speaker, Dynamic Mallocation (Slab Caches, Kmalloc)
3. Scheduling (Round Robin)
4. Multi Terminals 
5. Syscall (Read, Write, Open, Close, Execute)
6. File System (Read and Write)
   
[More Details](https://zichengma.github.io/ZinixOS/)


## Detailed Implementation

[A detailed implementation](https://zichengma.github.io/ECE391-MP3-Tutorial/)



## File Structure

### Booting
<br>
<table>
<thead>
<tr>
<th>File</th>
<th>Content</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>mp3.img</code><br><code>bootimg</code><br><code>filesys_img</code></td>
<td>Boot images</td>
</tr>
<tr>
<td><code>boot.S</code></td>
<td>First instructions of OS</td>
</tr>
<tr>
<td><code>kernel.c</code></td>
<td>Contains <code>entry</code> called from ^</td>
</tr>
<tr>
<td><code>x86_desc.S</code></td>
<td>Descriptor organization</td>
</tr>
<tr>
<td><code>multiboot.h</code></td>
<td>GRUB compliance</td>
</tr>
</tbody>
</table>
<br>

</table>
<h3 id="checkpoint-1">Checkpoint 1</h3>
<table>
<table>
<thead>
<tr>
<th>File</th>
<th>Content</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>idt.c</code><br><code>idt_lnk.S</code></td>
<td>IDT initialization<br>with assembly linkage</td>
</tr>
<tr>
<td><code>i8259.c</code></td>
<td>PIC chip driver</td>
</tr>
<tr>
<td><code>keyboard.c</code></td>
<td>Keyboard driver</td>
</tr>
<tr>
<td><code>rtc.c</code></td>
<td>RTC driver</td>
</tr>
<tr>
<td><code>page.c</code></td>
<td>Paging settings</td>
</tr>
</tbody>
</table>
<h3 id="checkpoint-2">Checkpoint 2</h3>
<table>
<thead>
<tr>
<th>File</th>
<th>Content</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>terminal.c</code></td>
<td>Terminal driver</td>
</tr>
<tr>
<td><code>filesys.c</code></td>
<td>Read-only file system</td>
</tr>
</tbody>
</table>
<h3 id="checkpoint-3--4">Checkpoint 3 &amp; 4</h3>
<table>
<thead>
<tr>
<th>File</th>
<th>Content</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>syscall.c</code></td>
<td>System call handler</td>
</tr>
</tbody>
</table>
<h3 id="checkpoint-5">Checkpoint 5</h3>
<table>
<thead>
<tr>
<th>File</th>
<th>Content</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>scheduler.c</code></td>
<td>Round-robin scheduler</td>
</tr>
<tr>
<td><code>pit.c</code></td>
<td>PIT chip driver</td>
</tr>
</tbody>
</table>
<h3 id="extra-credit">Extra Credit</h3>
<table>
<thead>
<tr>
<th>File</th>
<th>Content</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>memory.c</code></td>
<td>Dynamic memory allocation</td>
</tr>
<tr>
<td><code>speaker.c</code></td>
<td>Speaker driver</td>
</tr>
<tr>
<td><code>signal.c</code><br><code>signal_lnk.S</code></td>
<td>Signal handler</td>
</tr>
<tr>
<td><code>ata.c</code></td>
<td>ATA hard disk driver</td>
</tr>
</tbody>
</table>
<h3 id="helpers">Helpers</h3>
<table>
<thead>
<tr>
<th>File</th>
<th>Content</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>tests.c</code></td>
<td>Test points</td>
</tr>
<tr>
<td><code>lib.c</code></td>
<td>Basic library functions</td>
</tr>
<tr>
<td><code>types.h</code></td>
<td>Type declarations</td>
</tr>
<tr>
<td><code>debug.h</code><br><code>debug.sh</code></td>
<td>Useful debugging<br>macros and script</td>
</tr>
</tbody>
</table>
<br>
<hr>
<h2 id="testing-guidelines">Testing Guidelines</h2>
<p><em><strong>IMPORTANT</strong>: Backup <code>mp3.img</code> in a safe place and restore it each time the OS crashes.</em></p>
<h3 id="checkpoint-1">Checkpoint 1</h3>
<table>
<thead>
<tr>
<th>Type</th>
<th>Tested Functionality</th>
<th>Testing Method</th>
</tr>
</thead>
<tbody>
<tr>
<td>IDT</td>
<td>Values contained in IDT array</td>
<td>Run <code>idt_test</code></td>
</tr>
<tr>
<td>IDT</td>
<td>Handling exceptions</td>
<td>Run <code>div0_test</code><br><em><strong>(This should come last)</strong></em></td>
</tr>
<tr>
<td>Keyboard</td>
<td>Interpreting scancodes in<br>the keyboard handler</td>
<td>Type &amp; echo characters<br>on the screen</td>
</tr>
<tr>
<td>RTC</td>
<td>Receiving an RTC interrupt</td>
<td>Add <code>test_interrupt();</code><br>to <code>rtc.c:76</code></td>
</tr>
<tr>
<td>Paging</td>
<td>Values contained in<br>paging structures</td>
<td>In QEMU, Ctrl+Alt+2<br>and type <code>info mem</code></td>
</tr>
<tr>
<td>Paging</td>
<td>Dereferencing address ranges</td>
<td>Run <code>page_test</code> and<br><code>..._deref_not_exist</code></td>
</tr>
<tr>
<td>Paging</td>
<td>Dereferencing NULL to<br>produce a page fault</td>
<td>Run <code>..._deref_null</code></td>
</tr>
</tbody>
</table>
<h3 id="checkpoint-2">Checkpoint 2</h3>
<table>
<thead>
<tr>
<th>Type</th>
<th>Tested Functionality</th>
<th>Testing Method</th>
</tr>
</thead>
<tbody>
<tr>
<td>RTC</td>
<td>Change all possible frequencies<br>and receive interrupts</td>
<td>Run <code>rtc_driver_test</code></td>
</tr>
<tr>
<td>Terminal</td>
<td>Read user input from keyboard</td>
<td>Run <code>terminal_kbd_test_echo</code>,<br>type characters, press Enter,<br>and examine the echoed string</td>
</tr>
<tr>
<td>Terminal</td>
<td>Write different sized strings<br>to the terminal</td>
<td>Run <code>terminal_kbd_test_newline</code><br>and examine the echoed string<br>(Also change <code>nbytes</code> in argument)</td>
</tr>
<tr>
<td>Filesys</td>
<td>Check whether a file exists</td>
<td>Run <code>read_file_name_test</code></td>
</tr>
<tr>
<td>Filesys</td>
<td>Print out the contents of<br>different sized files</td>
<td>Run <code>read_data_test</code><br>and examine the echoed string</td>
</tr>
<tr>
<td>Filesys</td>
<td>Print out a list of all files<br>in the file system</td>
<td>Run <code>read_directory_test</code></td>
</tr>
<tr>
<td>Filesys</td>
<td>Filesys driver functions</td>
<td>Run <code>*_file_test</code> (For now,<br>they should all PASS except for<br>nonexistant filenames in <code>read</code>)</td>
</tr>
</tbody>
</table>
<h3 id="checkpoint-3">Checkpoint 3</h3>
<table>
<thead>
<tr>
<th>Type</th>
<th>Tested Functionality</th>
<th>Testing Method</th>
</tr>
</thead>
<tbody>
<tr>
<td>Syscall</td>
<td>Sanity check</td>
<td>Run <code>syserr</code> (#6 will fail since <code>vidmap</code><br>is not implemented)</td>
</tr>
<tr>
<td>Syscall</td>
<td>Execute a program</td>
<td>Run <code>shell</code></td>
</tr>
<tr>
<td>Syscall</td>
<td>Halt a program</td>
<td>Run <code>exit</code></td>
</tr>
<tr>
<td>Syscall</td>
<td>Read from the terminal</td>
<td>Run <code>hello</code></td>
</tr>
<tr>
<td>Syscall</td>
<td>Write to the terminal</td>
<td>Run <code>testprint</code>, <code>counter</code>, <code>pingpong</code></td>
</tr>
</tbody>
</table>
<h3 id="checkpoint-4">Checkpoint 4</h3>
<table>
<thead>
<tr>
<th>Type</th>
<th>Tested Functionality</th>
<th>Testing Method</th>
</tr>
</thead>
<tbody>
<tr>
<td>Syscall</td>
<td>Multiple shells</td>
<td>Run <code>shell</code> on top of another</td>
</tr>
<tr>
<td>Syscall</td>
<td>Get arguments</td>
<td>Run <code>cat &lt;filename&gt;</code></td>
</tr>
<tr>
<td>Syscall</td>
<td>Map video memory</td>
<td>Run <code>fish</code></td>
</tr>
</tbody>
</table>

</table>
<h3 id="checkpoint-4">Work Distribution</h3>
<table>

<table>
    <tr> <td rowspan="2"> <b>Checkpoint</b> </td> <td rowspan="2"> <b>Functionality</b> </td> <td rowspan="2"> <b>Branch</b> </td> <td colspan="4" align="center"> <b>Group Members</b> </td> </tr>
    <tr> <td> <b>Ziyuan Chen</b> </td> <td> <b>Zhirong Chen</b> </td> <td> <b>Zicheng Ma</b> </td> <td> <b>Shihua Zeng</b> </td> </tr>
    <tr> <td rowspan="3"> <b>Ckpt. 1</b> </td> <td> GDT, IDT </td> <td> <code>gdt_idt</code> </td> <td align="center"> ○ </td> <td align="center"> </td> <td align="center"> </td> <td align="center"> </td> </tr>
    <tr> <td> Init Devices </td> <td> <code>device</code> </td> <td align="center"> </td> <td align="center"> </td> <td align="center"> ○ </td> <td align="center"> ○ </td> </tr>
    <tr> <td> Init Paging </td> <td> <code>paging</code> </td> <td align="center"> </td> <td align="center"> ○ </td> <td align="center"> </td> <td align="center"> ○ </td> </tr>
    <tr> <td rowspan="3"> <b>Ckpt. 2</b> </td> <td> RTC Driver </td> <td> <code>rtc_driver</code> </td> <td align="center"> ○ </td> <td align="center"> </td> <td align="center"> </td> <td align="center"> </td> </tr>
    <tr> <td> Terminal </td> <td> <code>terminal_kbd</code> </td> <td align="center"> ○ </td> <td align="center"> </td> <td align="center"> </td> <td align="center"> ○ </td> </tr>
    <tr> <td> File System </td> <td> <code>filesystem</code> </td> <td align="center"> </td> <td align="center"> ○ </td> <td align="center"> ○ </td> <td align="center"> </td> </tr>
    <tr> <td rowspan="2"> <b>Ckpt. 3</b> </td> <td> Syscall (EH) </td> <td> <code>syscall_eh</code> </td> <td align="center"> ○ </td> <td align="center"> </td> <td align="center"> ○ </td> <td align="center"> </td> </tr>
    <tr> <td> Syscall (OCRW) </td> <td> <code>syscall_eh</code> </td> <td align="center"> </td> <td align="center"> ○ </td> <td align="center"> </td> <td align="center"> ○ </td> </tr>
    <tr> <td rowspan="1"> <b>Ckpt. 4</b> </td> <td> Syscall (GV) </td> <td> <code>master</code> </td> <td align="center"> </td> <td align="center"> </td> <td align="center"> </td> <td align="center"> ○ </td> </tr>
    <tr> <td rowspan="2"> <b>Ckpt. 5</b> </td> <td> Multi Terminals </td> <td> <code>multi_terminal</code> </td> <td align="center"> ○ </td> <td align="center"> </td> <td align="center"> ○ </td> <td align="center"> </td> </tr>
    <tr> <td> Scheduling </td> <td> <code>multi_terminal</code> </td> <td align="center"> </td> <td align="center"> ○ </td> <td align="center"> ○ </td> <td align="center"> ○ </td> </tr>
    <tr> <td rowspan="4"> <b>Extra</b> </td> <td> Dynamic Malloc </td> <td> <code>memory</code> </td> <td align="center"> </td> <td align="center"> </td> <td align="center"> ○ </td> <td align="center"> </td> </tr>
    <tr> <td> Speaker </td> <td> <code>multi_terminal</code> </td> <td align="center"> ○ </td> <td align="center"> </td> <td align="center"> </td> <td align="center"> </td> </tr>
    <tr> <td> Signal </td> <td> <code>signal</code> </td> <td align="center"> </td> <td align="center"> ○ </td> <td align="center"> </td> <td align="center"> </td> </tr>
    <tr> <td> File Write </td> <td> <code>filesystem</code> </td> <td align="center"> </td> <td align="center"> ○ </td> <td align="center"> </td> <td align="center"> ○ </td> </tr>
</table>

<br>
