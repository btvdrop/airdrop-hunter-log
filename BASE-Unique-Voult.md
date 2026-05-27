// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BaseUniqueVault {
    uint256 public totalDeposited;

    function deposit(uint256 amount) public {
        totalDeposited += amount;
    }

    function getTotal() public view returns (uint256) {
        return totalDeposited;
    }
}
