# How to capture physical memory to disk

There are multiple methods of capturing physical memory to disk. Here are some ways to do it 🙂

# 1. Using AccessData FTK Imager

This is my go to for newer version of Windows (eg 10). What's FTK Imager?

> FTK® Imager is a data preview and imaging tool that lets you quickly assess electronic evidence to determine if further analysis with a forensic tool such as AccessData® Forensic Toolkit® (FTK) is warranted.

And it's free. You can download it from here: 

[FTK Imager version 4.3.1.1](https://accessdata.com/product-download/ftk-imager-version-4-3-1-1)

After you download it and install it, go to **File > Capture Memory** 

![How%20to%20capture%20physical%20memory%20to%20disk/Untitled.png](How%20to%20capture%20physical%20memory%20to%20disk/Untitled.png)

This will pop up:

![How%20to%20capture%20physical%20memory%20to%20disk/Untitled%201.png](How%20to%20capture%20physical%20memory%20to%20disk/Untitled%201.png)

Select the destination path and click capture memory and wait.

Congrats, you just dumped the physical memory to disk!

# 2. DumpIt

My second go to for older windows xp machines

I download it from here:

[thimbleweed/All-In-USB](https://github.com/thimbleweed/All-In-USB/tree/master/utilities/DumpIt)

This one is very easy to use. Simply double click the executable and press **Y** on your keyboard. ****

![How%20to%20capture%20physical%20memory%20to%20disk/Untitled%202.png](How%20to%20capture%20physical%20memory%20to%20disk/Untitled%202.png)

This will be saved as a .raw file.