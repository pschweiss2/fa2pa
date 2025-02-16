<p align="center">
  <img src="https://github.com/user-attachments/assets/d67fde7f-2ff1-47e1-afc3-80312cf70469" alt="fa2pa_icon" width="300">
</p>
<p style="font-size: 32px;" align="center"><strong>A Community guide on using the HTTPS connector in FormAssembly to send form data to Microsoft's Power Automate</strong></p>
<hr style="border: 2px solid black; width: 50%;">

### A little bit of background
90% of the data we collected via FormAssembly was integrated to Salesforce in one way or another. I had a project come up that required a secure way to send data from a FormAssembly form over to Microsoft Power Automate for further processing. After a bunch of failed attempts at flight I finally lanaded on the following solution. *Full disclosure* I am certain there is probably more efficient way to do this on the Power Automate side, I welcome and appreciate any feedback! I beleive in the power of communtiy on projects like this and would love to collaborate with anyone who would like contribute. 

### Support My Work

If you find my work helpful, feel free to buy me a coffee!

[![Buy Me a Coffee](https://www.buymeacoffee.com/assets/img/custom_images/yellow_img.png)](https://www.buymeacoffee.com/petes)

<hr style="border: 2px solid black; width: 50%;">

### 1) Build your form in FormAssembly, for the sake of example we are using a basic contact us form in this example. 
<img src="https://github.com/user-attachments/assets/90c776fd-306c-4a34-b31f-ca0b7b6c81a4" alt="Description" width="500">

### 2) Setting up your HTTPs connector so the payload is easily identifiable on the other side. 
:fire: HOT tip - I always put my connectors on the After form submitted trigger, that way if there's an error you still capture all the data and can reprocess if necesary
By default each feild has an ambigious name (see below) I recommend updating this to more recognizable names, you'll thank me later on! </br>
<img src="https://github.com/user-attachments/assets/57c61830-6877-438d-8eff-3a24af30d4d5" width="800"> </br>
I recommend using logical names, see below...
<img src="https://github.com/user-attachments/assets/c8397fa2-c0c8-4962-bfc9-5e282a6697fc" width="800"> </br>
You can also add in variables outside of the form using the additional values area. See below.
<img src="https://github.com/user-attachments/assets/df75bdc6-e644-40a1-a1bd-3fa9733d0044" width="800"> </br>

### 3) Head over to Power Automate and create a new Automated Cloud Flow
Click skip, for some reason the HTTPS connector doesn't show up on the wizard screen. 
Give your flow a name and then "click Add a trigger". Search for https and click "When an HTTP request is received"
![image](https://github.com/user-attachments/assets/d8d0ad57-ee33-4a9c-8501-908724503a72)
