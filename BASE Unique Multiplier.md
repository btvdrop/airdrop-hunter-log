// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BaseUniqueMultiplier {
    uint256 public value = 1;

    function multiply(uint256 factor) public {
        value *= factor;
    }

    function resetValue() public {
        value = 1;
    }

    function getValue() public view returns (uint256) {
        return value;
    }
}
