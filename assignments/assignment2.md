# Assignment 2: Functional Design

## Problem Statement
**Problem Domain**: Desk Resource System of Baker House, Baker Home
Baker House is a student dormitory on campus that resides within Dorm Row. I have lived within Baker for the last two years on campus, am a Resident Peer Mentor, and am (recently) ex-Baker External president. With such a high involvement in the community, I am inclined to want the residents of Baker House to enjoy their time in the community, and utilize the resources the Baker Exec provides. As a resident, I want items to be available and easy to access. On the Baker Exec side, items and resources are purchased through Baker Exec funds, which incentivizes both good record-keeping and keeping track of items through multiple check-out cycles.
**Problem** Items going missing. One key problem faced by users within this domain is that items oftentimes go missing, or are returned damaged or are lost in the deep recesses behind Baker desk. This prevents residents from checking out items in the future, and de-incentivizes Baker Exec from purchasing more items that might be helpful to the residents. One specific example that occured last year was the mysterious disappearance of the Baker sewing machine. It was checked out sometime during the second semester, but never returned. The Houseteam sent out multiple emails, residents asked around, but the machine was permenantly gone from the desk collection. A sewing machine is not cheap, this one likely cost the House a few hundred dollars, and it make crafts projects for students' classes and UROPs difficult to complete.
**Stakeholders**
- *Baker Residents* use items, and will (a nontrivial amount) forget to return items. Items being more available, as well as a more transparent system, will help residents in using the resources available to them.
- *Baker Exec* has quite a bit of responsibility for keeping the items behind desk stocked and available. Losing items eats into the budget of Baker Exec, and is a frustrating and recurrent problem now.
- *Desk Workers* disorganization can exacerbate the issue regarding a resident's inability to find items. Additionally, if a system is difficult to use, it disincentivizes usage of the system, leaving the actual record-keeping aspect of the system disorganized.
- *Baker Houseteam* wants to be able to know where the lost items are going, and be able to track them down / manage which items are checked out.
**Evidence and Comparables**
- Evidence: [Baker Exec meeting notes](), discussing the need to restock the kitchen items behind desk since they have been stolen/not returned yet agin.
- Evidence: [DormCon meeting notes](), discussing what each dorm is doing in regards to checking out items from behind desk.
- Comparables: [StarRez] (no link available), the current system for checking out items behind desk, though the system is primarily geared towards logging packages. As it stands, I have received several first-hand accounts that this system is not a good solution, and is too focused on the package-aspect that it is trying to promote.
- Comparables: [Baker Room Reservation System](), which is just a Google Form. The reservation system is adjacent to the Desk Items system, but it is the closest comparable that Baker has currently, outside of the StarRex system. This does not display 
- Comparable: [U-Haul Reservation](https://www.uhaul.com/Locations/Truck-Rentals-near-Cambridge-MA-02139/837051/) The U-Haul reservation would be a comparable site, with flags / categories to search through. However, this sytem filters results too much -- scrolling through all of the offerings should be a feature within this desk items system.

## Application Pitch
**Title** Baker Belongings  
**Slogan** "There's GNOME need to worry with this inventory!"  
**Motivation** My application will solve this problem of items disappearing from behind desk, and minimize the burden of bookkeeping placed upon deskworkers by createing an accessible, long-lasting, and easily maintained checkout sysstem for Baker items.  
**Key Features**  
2. Check-in/Check-out system for items. This will allow Baker Exec to keep track of items, and for Baker Residents to be able to see what items have been checked out, and which ones are available to check out in the moment.
1. (RBAC) Role-Based Access Control of inventory-related actions. This will allow desk workers access to check-in/out items from behind desk, but prevent Baker Residents from illegally modifying the inventory. Additonally, this will allow members of the Houseteam and Baker Exec to view what items have been checked out, as well as the kerbs of the people who have checked out items. This will allow them to reach out to students who checked out big-ticket items a length of time in the past in order to keep track of where these items have ended up, and potentially retrieve them as well. This can help item stay available (aka, be returned after a resident is done using them), and allow for these items to be used in the years to come.  
3. Automatic Reminders. Residents who check out items can be sent automatic emails at predetermined intervals to remind them to return an item to the desk. This intervals can be predetermined by Exec and the Houseteam based on how expensive / important the item is.  

## Concept Design
### Concepts
1. Roles
    **concept** Roles  
    **purpose** maintain security of what actions different types of users can perform    
    **principle** A User can be a part of a specific role that can perform a given set of actions. Users can be promoted and demoted depending on need.    
    **state**  
    a set of Roles with  
        a Permission Flag  
        a set of Users  
    a set of Permission Flags with  
        a set of Actions  
**actions**   
    promteUser (user: User, permission; Permission Flag)  
        **requires** user is a valid User, permission is a valid Permission Flag  
        **effects** adds user to Role containing given Permission Flag  
    demoteUser (user: User, permission; Permission Flag)  
        **requires** user is a valid User, permission is a valid Permission Flag, user is within the role permission refers to  
        **effects** removes user from Role containing given Permission Flag  
    allowAction (user: User, action: Action): Boolean  
        **requires** user is a valid User, action is a valid Action  
        **effects** returns True if action is an action corresponding to the user's permission flags  
2. Checkout
    **concept** Reservation  
    **purpose** keep track of when items will expire, and send emails to users with expired items  
    **principle** when an item is checked out, set the item with the expiry time a predetermined time before and the kerb  
    **state**  
    a set of Items with
        an exiry Date
        a kerb String
    **actions**   
    checkoutItem(kerb: String, item: Item)
        **requires** item a valid item, kerb is a resident in the Roles
        **effects** an expiry Date is set   
    checkinItem(item: Item)  
        **requires** item is in the set of checked out items  
        **effects** removes item from set of checked out items  
    notifyCheckout()
        **effects** sends an email to the kerb as a reminder to check the item back in
3. ViewItems
    **concept** View Items  
    **purpose** allow for stakeholders to see information about items within the inventory  
    **principle** information about the set of items can be available upon request, seeing the status of a single item, or searching through specific flags or categories   
    **state**
    a set of Items with
        a itemName String
        a lastCheckout Date 
        a available Flag
        a lastKerb String 
        a set of Categories
    **actions**
    viewAvailable() : a set of Items  
        **effects** returns the subset of items that are available  
    viewCategory(category: String) : a set of Items  
        **effects** returns the subset of items that have category in their set of Categories   

    viewLastCheckedoutDate(itemName: String) : a Date  
        **requires** item is a valid item within the state  
        **effects** returns a date of last checkout  

    viewLastCheckedoutFull(itemName: String) : a Date   
        **requires** item is a valid item within the state  
        **effects** returns a date of last checkout  

4. Authorization
**concept** Authorization  
**purpose** Items behind desk, and especially information about who has check out what item, should be kept hidden and available only to specific Users.  
**principle** After a user registers with a username and a password,  
    they can authenticate with that same username and password  
    and be treated each time as the same user  
  **state**  
    a set of Users with  
        a Username  
        a Password
        an Email  
        a Role  
  **actions**  
    register (username: String, password: String, email: String): (user: User)  
      **requires** username does not already exist  
      **effects** creates a new User with this username, password, and email, generates and sends the secure token to the email, and flags this User as unconfirmed  
    authenticate (username: String, password: String): (user: User)  
      **requires** a User with the matching username and password exist  
      **effects** returns the matching User  
    confirm (username: String, token: String): (user: User)  
      **requires** username is within the set of Users, token matches the secret Token  
      **effects** User's flag is now set to confirmed, the registration is complete   
  
### Synchronizations
  
**sync** QueryItems  
**when** Authorization.authenticate(username, password) : (user: User)  
**then** Roles.allowAction(user, viewAvailable) : Boolean
ViewItems.viewAvailable() : a set of Items   
  
**sync** UpdateRoles   
**when** Authorization.register(username, password) : (user: User)      
**then** Roles.promoteUser(user, permission; Permission Flag)  
     
**sync** CheckoutItem    
**when** Authorization.authenticate(username, password) : (user: User)  
**then** Roles.allowAction(user, checkoutItem) : Boolean  
Checkout.checkoutItem(kerb: String, item: Item)  

These concepts play a role in maintaining transparency of the Desk Items inventory system, as well as keeping users on top of the items that they have checked out. This also allows for several levels of users to be aware of which items are currently available, instead of just figuring out status of items from hearsay or trying to check out items and failing.

## UI Sketches
![UI Sketches](/assets/UI_view.png)

## User Journey
Louis is a resident of Baker, and has been working very hard on his school work this semester, but wants a break from the endless grind of psets. Lucky for him, it's the middle of a long weekend, and he has time to spend not focusing on school. In order to clear his mind, Louis wants to play tennis on the courts just outside Baker, but the Z center is closed. He is just in luck! Louis remembers hearing that Baker has a wealth of items behind desk for Baker residents to check out and use.   

Louis decides to check the Baker Belongings inventory to see what Baker Desk has available. He logs into the site, with his easily-remembered username and password, and clicks down to the Residents inventory view. Scrolling through the list he is surprised! There are a ton of items available for checkout. It's a little overwhelming, so he uses the 'Sports' flag to filter the inventory results. Only a hanful of items are displayed now. He scolls past the ice skates and the spikeball nets. There's what he's looking for! The tennic rackets are available for checkout. Pleased, Louis heads down from his room to Baker Desk.   

Arriving at Baker Desk, Louis sees his friend Daisy on shift. After exchanging greetings, he asks Daisy if he can check out two rackets (one for his friend Herby) from behind desk. "Of course!" Daisy says, "Let me just see your school id." Daisy searches through the catalog of desk items, finding the tennis rackets quickly. She clicks 'Checkout', and inputs Louis's kerb, which autofills by Daisy typing in his name. Just like that, Louis has checked out his rackets.   


Fast-forward a few days. Louis had a great time playing tennis, but accidently forgot to return the tennis rackets when he got back to Baker. A member of the housteam, Geraldine, now wants to check out the tennis rackets. She sees that Louis forgot to return the rackets, and contacts him directly. He is able to return the rackets just in time, and will remember to return them sooner for the next time!  
