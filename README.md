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

### Here's the nuts and bolts of the project.
Before we get to the How To, here's a couple of items I am working to figure out in future iterations of this.
* I ran this as "Anyone can trigger the flow". I wasn't ready to mess with authentication, my use case didn't demand it, but I would like to figure that out. 

### Now the good stuff! 

### 1) Build your form in FormAssembly, for the sake of example we are using a basic contact us form in this example. 
<img src="https://github.com/user-attachments/assets/90c776fd-306c-4a34-b31f-ca0b7b6c81a4" alt="Description" width="500">

### 2) Setting up your HTTPs connector so the payload is easily identifiable on the other side. 
:fire: HOT tip - I always put my connectors on the After form submitted trigger, that way if there's an error you still capture all the data and can reprocess if necesary
By default each feild has an ambigious name (see below) I recommend updating this to more recognizable names, you'll thank me later on! </br>
<img src="https://github.com/user-attachments/assets/57c61830-6877-438d-8eff-3a24af30d4d5" width="800"> </br>
I recommend using logical names, see below. </br>
<img src="https://github.com/user-attachments/assets/7b0a4e60-0be3-4b19-8bad-479a2cdf891b" width="800">/img>

You can also add in variables outside of the form using the additional values area. See below.
<img src="https://github.com/user-attachments/assets/df75bdc6-e644-40a1-a1bd-3fa9733d0044" width="800"> </br>

### 3) Head over to Power Automate and create a new Automated Cloud Flow
Click skip, for some reason the HTTPS connector doesn't show up on the wizard screen. 
Give your flow a name and then "click Add a trigger". Search for https and click "When an HTTP request is received" </br>
<img src="https://github.com/user-attachments/assets/d8d0ad57-ee33-4a9c-8501-908724503a72" width="450"> </br>
By defualt the HTTP URL won't populate, once you save you will get a URL to use, but for you can save we need to add atleast one element to the flow. Go ahead and add a Data Operation/Compose element. Use the Body from the trigger element.
<img src="https://github.com/user-attachments/assets/fae1286d-2cb2-46a8-8f93-ab963e339b6f" width="400"> </br>
This grabs the content from the body of the trigger and makes it useable in the flow.

### 4) Now we need to start breaking down the contents of the form data.
Here's the first gotcha, Power Automate has this great Parse JSON connector that you can throw a schema into and it spits out perfectly useable variables. In this case FormAssembly sends the data over as application/x-www-form-urlencoded, so there's a few more steps we need to do first. 
Add another Compose element and this time insert a string expression like this string(outputs('composeBody')) note that the composeBody is the name of the fist Compose element. If you names yours differently then adjust accodringly, or use the Dynamic content box to drop in this element.

### 5) Let's find those form fields we named earlier
To get the fields and start working with them we will add another Compose element and use the split expression, here's an example: split(outputs('convertToString'), '&'). This splites every time a & appears. Now we can start looking for those fields we defiend earlier. For this we need a Control / Apply to each element for "Select an output from the previous steps" use the output from the compose element we created previously.
Now we have the form data in a useable format, we can start turning the form data into variables, for this you need a Control / Switch element INSIDE of the Apply to each element.
For what to switch On - You need to split your data again to find the field Names, using the expression builder to split with the = as the delimiter and the [0] tells the expression you want the first element of the array that is returend. In short this is saying split the items from the Apply to each loop and give me what's on the left hand side of the = sign.

### 6) Initialize your variables
We need to initialize all the variables we will need prior to parsing the form content. Add a Variable / Initialize Variable element for of each of the form variables to be extracted. In my use case they are all type String, but you can adjust that based on your use case. 
![image](https://github.com/user-attachments/assets/99e7ff82-1756-4468-94b3-2c65455cb121)


### 7) Set your variables 
Here's the part that depending on the size of your form can get tedious and full disclosure, there is most likely a more efficient way to do this, but this is what worked for me. Please feel free to comment with any ideas here! 
Inside the switch element we will define each field name and what do it if that field name is found in the loop. To do this click the plus icon under the Switch. In the picture below we are talking about the plus icon on the LEFT </br>
<img src="https://github.com/user-attachments/assets/8c515fde-3946-4d5b-a54f-c9a141dc1add" width="300"> </br>
The paramters for each case is going to be the names we defined in step 2, I also recommend renaming the Switch case to the same thing, this makes the whole flow much cleaner. </br>
<img src="https://github.com/user-attachments/assets/904785fe-5d52-4e16-8d30-c20849e92d05" width="300"> </br>
Repeate these steps for each of the field names defined in step 2. </br>
Now you will have a Switch element with all our cases defined. </br>
<img src="https://github.com/user-attachments/assets/06329a8b-8b90-4912-b653-9b8071a97b5b" width="800"> </br>
Now for each switch case we will add a Variable / Set Variable element, we will use a similar split expression like we did in the previous step except we want the second element in the array for this we will use [1] to get it. 
<img src="https://github.com/user-attachments/assets/a454bc13-f033-4592-9c63-91b9c9667e44" width="800"> </img>

Repeat this for each switch case.
Your switch element will look like this at the end. 
![image](https://github.com/user-attachments/assets/82eab344-66e3-4022-8aa6-200e9a8e5d6f)


### 7) Do WHAT EVER you want!
From here you now have usable variables you can use to drive any number of different actions.

You will end up with a flow that looks something like tthe below. For the sake of example I put a Outlook and Teams element here, but you can use this data now with virtually anything that connects to Power Automate.
![image](https://github.com/user-attachments/assets/e5ae74c3-0d0c-4859-a5c0-18c8bacb9d0e)
