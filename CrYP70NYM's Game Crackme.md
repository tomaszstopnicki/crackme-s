CrYP70NYM's Game Crackme.

Crackme file link: https://crackmes.one/crackme/66a877d290c4c2830c8213f0

Zip password: crackmes.one


Let’s try to run this file first.

 ![image](https://github.com/user-attachments/assets/2ba4d268-c3f2-4b63-b57e-e8d039dcc637)


The messagebox tells us that CD-ROM is not inserted.

Basic static analysis tells us at first that the file is packed with popular UPX 3.08, so to analyze it more further we need to unpack it first.
To unpack the UPX we have a few options:
•	Use an UPX unpacker (the easiest, automated method that might work with most of UPX versions)
•	Manual unpacking using debugger’s breakpoint to jump into unpacked data (I recommend this one since it’s good to know how things work).


After unpacking let’s put it into IDA.

![image](https://github.com/user-attachments/assets/0a0f3613-2c24-4d3e-a3aa-b1e60278a5a1)


What caught my eye is a call to **checkCdRom**. It’s connected to the error we saw when we first ran the crackme. Let’s investigate it:

![image](https://github.com/user-attachments/assets/2e91c676-016d-4fa3-bc1e-9e05b87ba389)


This subroutine calls a **GetDriveTypeA**, a function to detect whether a specific disk is a removable, fixed, CD-ROM, RAM disk or network drive.

Few lines later it compares the result with **5** which is **DRIVE_CDROM**. If the comparison is successful then it searches for a file Disk1.iso in C drive.

If one of the statements is unsuccessful then it shows a messagebox seen previously telling us that CD-ROM not inserted.

Now when we understood the logic behind it, let’s run it in OllyDbg to skip the checking part.

After we run the crackme in debugger let’s search for the error string “CD-ROM Not Inserted”

![image](https://github.com/user-attachments/assets/a743da3f-05c5-4ff0-bcb0-4602d4478177)

 
Before the message is displayed we have a **JE** (Jump Equal) statement at **0x401558** that checks for the result from checkCdRom we investigated earlier.

All we have to do is to either change the instruction from  **JE** to **JMP** to skip the CD-ROM checking part or fill the JMP instruction at **0x401588** with **NOP’s**. The second way will still show the warning, but the crackme will continue.

Now all we have to do is to run the crackme directly from the debugger with our changed instructions or patch the file and run it without debugger

After we skipped the detection part we can enjoy a maze!




