// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BaseUniqueLogger {
    string public lastLog;
    uint256 public lastTimestamp;

    function log(string memory message) public {
        lastLog = message;
        lastTimestamp = block.timestamp;
    }

    function getLastLog() public view returns (string memory, uint256) {
        return (lastLog, lastTimestamp);
    }
}
