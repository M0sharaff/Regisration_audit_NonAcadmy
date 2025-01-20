### 1. M-01 : `countTotalReferrals` recursive call can create OOG revert 
 ```solidity 
    function countTotalReferrals(address user) internal view returns (uint256) {//@ lead @ok loop ?? recursion works fine as the order is controlled ! 
        uint256 totalReferrals = users[user].referrals.length;//@audit naming convention is poor ! 
        
        for (uint256 i = 0; i < users[user].referrals.length; i++) {
            totalReferrals += countTotalReferrals(users[user].referrals[i]);
        }
        
        return totalReferrals;
    }
 ```
  The view function incurs a gas cost of approximately 30000 gas for every 3 indirect referrals, with each recursive call consuming around 10000 gas. 
  Consequently, users with more than 14000 referrals may encounter an out-of-gas (OOG) revert when attempting to retrieve their referral count. 
  This scenario is possible  in the presence of a large user base, where the indirect referral count of early adopters may surpass 14000 due to the cumulative effect of direct and indirect referrals. 
  
  ### Recommendation : This is up to the developers to decide whether to implement a fix for this issue, as it does not have a fatal impact on the contract's functionality. However, it is recommended to consider the potential consequences of not addressing this issue, especially in scenarios with a large number of users.
### 2. L-01 : `getUserByUserId` and `getUserByReferrerId` both have the same functionality. `getUserByReferrerId` can be removed to reduce code size. 
 Both `getUserByUserId` and `getUserByReferrerId` have the same functionality, which is retrieving user data from the user ID. So, one of them can be removed. 
 
### 3. Informational-01 : In the `registerUser` function, usernames can be duplicate. 
 Usernames are supposed to be unique, but the `registerUser` function does not ensure that. Though implementing this is not efficient at the smart contract level, I suggest ensuring that in the frontend.  
