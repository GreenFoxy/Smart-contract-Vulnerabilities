BattleToken
---------------
https://etherscan.io/address/0x4daa9dc438a77bd59e8a43c6d46cbfe84cd04255#code

CVE-2018-17882

Integer Overflow
  
    
    function batchTransfer(address[] _to, uint _value) public {
        balances[msg.sender] = safeSub(
            balances[msg.sender], _to.length * _value
        );
        for (uint i = 0; i < _to.length; i++) {
            balances[_to[i]] = safeAdd(balances[_to[i]], _value);
            Transfer(msg.sender, _to[i], _value);
        }
    }


If calls this function with _to.length = 2, and _value = 2^255, it will cause an integer overflow at line 3, bypass the SafeSub() at line 2 and finally transfer 2^255 tokens to attacker's accounts.

The founder might restrict the _value to uint, the maximum of which is 2^32 in normal case, for preventing the mul operation at line 3 overflows to 2^256. However, the compiler will treat uint as uint256 in solidity, so the vulnerability still exists.
