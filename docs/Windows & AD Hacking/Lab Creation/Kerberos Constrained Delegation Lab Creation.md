# Kerberos Constrained Delegation Lab Creation

Creating a lab for practicing Constrained Kerberos Delegation is very easy.

# Creating the User

First you will create a new user in your Domain, you can use an existing user also.

![Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled.png](Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled.png)

Give it a username and a password.

# Adding the SPN

To allow the user to do kerberos delegation, it has to have an SPN first. We are going to add a dummy one: `constrained/` 

![Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled%201.png](Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled%201.png)

# Enabling Delegation for CIFs and LDAP on our DC for our User

Go to the Delegation tab in the User properties and on Add

![Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled%202.png](Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled%202.png)

Next click Users and Computers and Choose your DC and click OK

![Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled%203.png](Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled%203.png)

Select CIFs and LDAP on DC

![Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled%204.png](Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled%204.png)

It should look like this in the end:

![Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled%205.png](Kerberos%20Constrained%20Delegation%20Lab%20Creation/Untitled%205.png)

Don't forget to set `Trust this user for delegation to specified services only` and then `Use any authentication protocol` also make sure the services are as following, you can now go try the attack!