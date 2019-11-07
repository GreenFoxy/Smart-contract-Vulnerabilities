https://etherscan.io/address/0x7367a68039d4704f30bfbf6d948020c3b07dfc59#code

Integer Overflow

CVE-2018-19460

    
    function creditEqually(address[] users, uint256 value) public onlyMaster returns (bool) {
        uint256 balance = balances[msg.sender];
        uint256 totalValue = users.length * value;      //(*)

        require(totalValue <= balance);                 //(**)
        balances[msg.sender] = balance - totalValue;

        address currentUser;
        uint256 currentOldBalance;
        uint256 currentNewBalance;
        for (uint256 i = 0; i < users.length; ++i) {
            currentUser = users[i];
            currentOldBalance = balances[currentUser];
            currentNewBalance = currentOldBalance + value;

            require(currentOldBalance <= currentNewBalance);
            balances[currentUser] = currentNewBalance;

            Transfer(msg.sender, currentUser, value);
        }

        return true;
    }

There exists a integer overflow at (\*). 

For example, if the master calls this function with *users.length* = 2, and *value* = 2^255, the *totalValue* will be small enough to bypass the check at (\*\*), and then result in transfering a large amounts of balance to arbitrary users.
