# 1. Explain what lives inside of an account.
    The contract(s) code and account storage live inside the account.

# 2. What is the difference between the /storage/, /public/, and /private/ paths? 
    - The /storage/ path contains all the data an account has. The only the auth account has access to this. 
    - The /public/ and /private/ paths have gated access depending on how the contract is set up (interfaces!)
      Anything in the /public/ path is available to everyone, only the auth account and those the auth account gives 
      permission can access the /private/ path. 
      **However, EVERYTHING IS VISIBLE ON THE BLOCKCHAIN. NEVER STORE SENSITIVE DATA (SEED PHRASE ETC.), EVEN IN THE PRIVATE PATH**

# 3. What does .save() do? What does .load() do? What does .borrow() do?
    - .save() takes two parameters and saves data to the /storage/ path
      .save(<- nameOfResource, to: /storage/MyPath)
      
    - .load() takes one parameter and takes the data out of storage
      .load<@Stuff.Type>(from: /storage/MyPath)
      
    - .borrow() let's us just look at data (using refs) using one parameter (same parameter as load)
      .borrow<&Stuff.Type>(from: /storage/MyPath)
      

# 4. Explain why we couldn't save something to our account storage inside of a script.
    - You can only access the account information within the prepare phase.

# 5. Explain why I couldn't save something to your account.
    - Only the auth account can save. 

# 6. Define a contract that returns a resource that has at least 1 field in it. Then, write 2 transactions:

    -A transaction that first saves the resource to account storage, then loads it out of account storage, logs a field inside the resource, and destroys it.
    
    
  Contract:
  
```javascript
    
        pub contract MyContract {

        pub resource MyResource{
         pub var name: String

         init(){
         self.name= "Ash"
         }
        }
        pub fun createMyResource(): @MyResource {
            return <- create MyResource()
        }

        }
```
    
   Save Transaction:
   
 ```javascript
    
       import MyContract from 0x01

        transaction {

          prepare(signer: AuthAccount) {
          let myTestResource <- MyContract.createMyResource()
          signer.save(<- myTestResource, to: /storage/MyPath)
          }
          execute {

          }
        }

 ```
 
   Load Transaction:
   
 ```javascript
 
   import MyContract from 0x01

    transaction {

      prepare(signer: AuthAccount) {
      let myTestResource <- signer.load<@MyContract.MyResource>(from: /storage/MyPath)
                            ?? panic("The resource does not exist")
      log(myTestResource.name)
      destroy myTestResource
      }
      execute {

      }
    }

 ```
 
 ![image](https://user-images.githubusercontent.com/100004665/157690060-cb91c712-35f9-4d96-8f4a-83732e58daac.png)
 

-A transaction that first saves the resource to account storage, then borrows a reference to it, and logs a field inside the resource.

Contract and Save Transaction are save as above.

Borrow Transaction:

 ```javascript
     import MyContract from 0x01

    transaction {

      prepare(signer: AuthAccount) {
      let myTestResource = signer.borrow<&MyContract.MyResource>(from: /storage/MyPath)
                            ?? panic("The resource does not exist")
      log(myTestResource.name)

      }
      execute {

      }
    }
 ```
 
 ![image](https://user-images.githubusercontent.com/100004665/157690558-f6858f02-1cc9-4495-bce8-4457c8794c5a.png)

