**JayC’s Crackme.**

Crackme file link: https://crackmes.one/crackme/66a64ff190c4c2830c8212da

Zip password: crackmes.one


We are given a file ‘crackme1.exe’. When we run it we get a console writeline with a message prompting for a key:
![image](https://github.com/user-attachments/assets/fa749f63-b266-42c2-8de2-1c070c34229a)



The file seems to not be packed in any way, so let’s put it against FLOSS.
![image](https://github.com/user-attachments/assets/6131ce7d-fb90-47eb-8dab-8151b25b87ab)

 

Nothing interesting here, strings found a SHA256, so it might give us a hint about some kind of encryption.
The crackme is written in .NET 4.0 and unfortunately I don’t have IDA Pro to decompile it, so let’s use a .NET Reflector to analyze it.
![image](https://github.com/user-attachments/assets/d7db5987-1cc2-428c-8d9c-6afaca92ff2c)

 

Basically the code is creating an object executable1 in a class Executable and then calling a Retrieve_Key function from that object. The key for our crackme is stored there.
![image](https://github.com/user-attachments/assets/d989afeb-af71-446e-a2ea-dfa185dcbfdd)

 
As we can see, the key is randomly generated each time it starts with use of RNGCryptoServiceProvider. First the bytes are randomly created, translated to string and put into two Environment Variables: “key” and “key2”. 

After the key is generated it is hashed by SHA256 and stored in variable **primaryKeyHash**.

Going back to the main, the user input is sent out to a function Check that does compare the input with **length**, **key**, **key2** and **primaryKeyHash**, if all of them are passed with success then  the Input Status is set to “Succes” and we should receive the text “Congrats you got the Passcode!”.

But how to get the passcode if it’s randomly generated each time the crackme runs?

Well, we have to run it in a .NET debugger, I will be using dnSpy version v6.1.8

Navigate to a call to **Retrieve_Key** function and set a breakpoint (F5) at the beginning of the function.
 ![image](https://github.com/user-attachments/assets/d988db5d-6213-4c90-88fa-a5aab23510e5)


Now run the debugger and press F10 few times to step over each line of code, at the bottom you should see a string.Replace returned and the value it holds, and that is our passcode.
 ![image](https://github.com/user-attachments/assets/fe3f4179-e1f3-4630-8036-933e3be6218a)


Continue the debugger by clicking F5 and enter the key in the console to check if it’s correct.
 ![image](https://github.com/user-attachments/assets/80cb3388-d83d-478c-a19e-3d6eefd97d7b)


The second option is to patch the crackme so it shows the key each time it get’s opened, to do that let’s navigate to Retrieve_Key, right click and click Edit Method (C#).

Now, let’s add a line of code that displays the generated key like on the image below:
 ![image](https://github.com/user-attachments/assets/312e3ff9-6583-4ed2-8241-65aa467bf3ce)


Press Compile, then save the executable by clicking File->Save module->OK.

Now when we run the patched executable the key is visible to us before the input.

 ![image](https://github.com/user-attachments/assets/c3715adc-834c-4f9d-82dc-efeacaa5e036)

