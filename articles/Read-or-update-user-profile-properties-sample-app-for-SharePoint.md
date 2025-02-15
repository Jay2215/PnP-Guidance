
# Read or update user profile properties sample add-in for SharePoint
You can use a provider-hosted add-in to read or update SharePoint single and multivalued user profile properties.

    
 _**Applies to:** Office 365 | SharePoint 2013 | SharePoint Online_

    
The [UserProfile.Manipulation.CSOM](https://github.com/OfficeDev/PnP/tree/master/Samples/UserProfile.Manipulation.CSOM) sample shows you how to read and update user profile properties for a particular user. This sample uses a provider-hosted add-in to:
    

- Read and display all user profile properties for a user.
    
- Update a single-valued user profile property.
    
- Update a multivalued user profile property.
    
Use this solution if you want to:

- Read or write data to a user profile property for a user. 
    
- Use user profile property values to personalize SharePoint.
    

**Note**  This code sample only runs on Office 365. 


## Before you begin
<a name="sectionSection0"> </a>

To get started, download the  [UserProfile.Manipulation.CSOM](https://github.com/OfficeDev/PnP/tree/master/Samples/UserProfile.Manipulation.CSOM) sample add-in from the [Office 365 Developer patterns and practices](https://github.com/OfficeDev/PnP/tree/dev) project on GitHub.

Before you run Scenario 1:


1. At the top of your Office 365 site, choose your profile picture, and then choose  **About me**, as shown in Figure 1. 
    
2. On the  **About me** page, choose **edit your profile**.
    
3. In  **About me**, enter I work at Contoso.
    
4. Choose  **Save all and close**. 
    
Before you run Scenario 3:


1. At the top of your site, choose your profile picture, and then choose  **About me**, as shown in Figure 1. 
    
2. On the  **About me** page, choose **edit your profile**.
    
3. On  **Edit Details**, choose  **Details**.
    
4. In  **Skills**, enter C#, JavaScript.
    
5. Choose  **Save all and close**. 
    

**Figure 1. Navigating to a user's profile page by choosing About me**

![Screenshot of the user's profile page with About me highlighted](media/692b9a82-45df-4c82-960b-42281be39f1c.png)


## Using the UserProfile.Manipulation.CSOM app
<a name="sectionSection1"> </a>

When you run this sample, a provider-hosted add-in starts, as shown in Figure 2.


**Figure 2. Start page of the UserProfile.Manipulation.CSOM app**

![Screenshot of the start page of the UserProfile.Manipulation.CSOM app](media/ec62daf7-1323-4a12-b2c4-a95e0312c58f.png)

This code sample includes three scenarios.




### Scenario 1: Read all user profile properties

When you choose  **Run scenario 1**, the add-in reads all user profile properties for the current user, and then displays the user profile data in  **Current user profile properties**, as shown in Figure 3.


**Figure 3. Current user's profile properties data**

![Screenshot of the current user's profile properties data](media/012475be-21d7-48b1-bce2-1e863e025c0d.png)

Choosing  **Run scenario 1** calls the **btnScenario1_Click** method in CodeSample1.aspx.cs to perform the following tasks:



- Use  **PeopleManager** to retrieve all the user profile properties for the current user.
    
- Iterate over  **PersonProperties.UserProfileProperties** to list the values of the user profile properties in a text box.
    

    
**Note**  The code in this article is provided as-is, without warranty of any kind, either express or implied, including any implied warranties of fitness for a particular purpose, merchantability, or non-infringement.




```C#
protected void btnScenario1_Click(object sender, EventArgs e)
        {

            var spContext = SharePointContextProvider.Current.GetSharePointContext(Context);

            using (var clientContext = spContext.CreateUserClientContextForSPHost())
            {
                // Get the people manager instance and load current properties.
                PeopleManager peopleManager = new PeopleManager(clientContext);
                PersonProperties personProperties = peopleManager.GetMyProperties();
                clientContext.Load(personProperties);
                clientContext.ExecuteQuery();

                // Output user profile properties to a text box.
                txtProperties.Text = "";
                foreach (var item in personProperties.UserProfileProperties)
                {
                    txtProperties.Text += string.Format("{0} - {1}{2}", item.Key, item.Value, Environment.NewLine);
                }
            }
        }

```


### Scenario 2: Update a single-valued user profile property

Scenario 2 shows how to update a single-valued user profile property. As shown in Figure 4, the current value of the  **About me** user profile property for the user running this add-in is **I work at Contoso**. To update the value of the  **About me** user profile property, in the **About me new value** box, enterI am a software engineer at Contoso and then choose **Run scenario 2**. The code updates the value of the  **About me** property to **I am a software engineer at Contoso**. As shown in Figure 5, the add-in updates  **About me current value** with the new value of the **About me** user profile property.


**Figure 4. Scenario 2 start page**

![Screenshot of the start page for Scenario 2](media/ca229e30-e418-4701-a991-cf669cbc7483.png)

**Figure 5. Updated About me user profile property**

![Screenshot of the updated About Me user profile property](media/9ffc34d9-b9b5-481c-8b63-fa28bc883d04.png)

Choosing  **Run scenario 2** calls the **btnScenario2_Click** method in CodeSample2.aspx.cs to do the following:



- Use  **PeopleManager** to get the user profile properties of the current user.
    
- Format the text entered by the user in HTML.
    
- Update the value of the  **AboutMe** user profile property by using **SetSingleValueProfileProperty**.  **SetSingleValueProfileProperty** accepts three parameters:
    
      - The account name of the user whose user profile you're updating.
    
  - The user profile property name ( **AboutMe** in this scenario).
    
  - The property value, in HTML format ( ** I am a software engineer at Contoso** in this scenario).
    



```C#
protected void btnScenario2_Click(object sender, EventArgs e)
        {
            var spContext = SharePointContextProvider.Current.GetSharePointContext(Context);

            using (var clientContext = spContext.CreateUserClientContextForSPHost())
            {
                // Get the people manager instance and initialize the account name.
                PeopleManager peopleManager = new PeopleManager(clientContext);
                PersonProperties personProperties = peopleManager.GetMyProperties();
                clientContext.Load(personProperties, p => p.AccountName);
                clientContext.ExecuteQuery();

                // Convert entry to HTML.
                string updatedValue = (txtAboutMe.Text).Replace(Environment.NewLine, "");

                // Update the AboutMe property for the user using account name from the user profile.
                peopleManager.SetSingleValueProfileProperty(personProperties.AccountName, "AboutMe", updatedValue);
                clientContext.ExecuteQuery();

            }
        }

```


    
**Note**  If you use custom user profile properties, configure the property to be editable by users. The technique used in this scenario will work for custom user profile properties. 


### Scenario 3: Update a multivalued user profile property

Scenario 3 shows how to update a multivalued user profile property. Figure 6 shows the start page for Scenario 3.  **Skills current value** shows the skills of the user running the app. The skills are read from the SPS-Skills user profile property for the user.


**Figure 6. Scenario 3 start page**

![Screenshot of the start page for Scenario 3](media/30926c45-3ecc-4c7d-bc27-d896fc6136b2.png)

To add new skills to the SPS-Skills user profile property from this add-in:



1. Enter HTML5, and then choose  **Add Skill**. 
    
2. Enter ASP.Net, and then choose  **Add Skill**. 
    
3. Choose  **Run scenario 3**.
    
4. Verify that  **Skills current value** shows the new list of skills for the user.
    
5. Verify that the  **SPS-Skills** user profile property for the user now shows the new list of skills.
    
Choosing  **Run scenario 3** calls **btnScenario3_Click** in CodeSample3.aspx.cs to do the following:



- Use  **PeopleManager** to get the user profile properties of the current user.
    
- Read the list of skills shown in the list box. 
    
- Save the new skills to the  **SPS-Skills** user profile property by using **SetMultiValuedProfileProperty**.  **SetMultiValuedProfileProperty** accepts three parameters:
    
      - The account name of the user whose user profile is being updated.
    
  - The user profile property name, which is  **SPS-Skills**.
    
  - The property values as a  **List** of string objects.
    



```C#
  protected void btnScenario3_Click(object sender, EventArgs e)
        {
            var spContext = SharePointContextProvider.Current.GetSharePointContext(Context);

            using (var clientContext = spContext.CreateUserClientContextForSPHost())
            {
                // Get the people manager instance and initialize the account name.
                PeopleManager peopleManager = new PeopleManager(clientContext);
                PersonProperties personProperties = peopleManager.GetMyProperties();
                clientContext.Load(personProperties, p => p.AccountName);
                clientContext.ExecuteQuery();

                // Collect the user's skills from the list box in order to update the user's profile.
                List<string> skills = new List<string>();
                for (int i = 0; i < lstSkills.Items.Count; i++)
                {
                    skills.Add(lstSkills.Items[i].Value);
                }

                // Update the SPS-Skills property for the user using account name from the user's profile.
                peopleManager.SetMultiValuedProfileProperty(personProperties.AccountName, "SPS-Skills", skills);
                clientContext.ExecuteQuery();

                // Refresh the values.
                RefreshUIValues();
            }

        }

```


## Additional resources
<a name="bk_addresources"> </a>


-  [User profile solutions for SharePoint 2013 and SharePoint Online](user-profile-solutions-for-sharepoint.md)
    
-  [ Search.PersonalizedResults sample](https://github.com/OfficeDev/PnP/tree/master/Samples/Search.PersonalizedResults)
    
