// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BASE_Daily_GM_Logger {
    address public owner;
    mapping(address => uint256) public lastGMTime;
    mapping(address => string) public lastGMMessage;
    uint256 public totalGMs;

    uint256 public constant GM_COOLDOWN = 1 days;  // 24時間に1回

    event GMLogged(address indexed user, string message, uint256 timestamp);

    constructor() {
        owner = msg.sender;
    }

    // 毎日GMをログに残す
    function sayGM(string memory message) external {
        require(block.timestamp >= lastGMTime[msg.sender] + GM_COOLDOWN, "Too soon");
        require(bytes(message).length > 0 && bytes(message).length <= 100, "Invalid message length");

        lastGMTime[msg.sender] = block.timestamp;
        lastGMMessage[msg.sender] = message;
        totalGMs++;

        emit GMLogged(msg.sender, message, block.timestamp);
    }

    // 自分の最後のGM確認（無料）
    function getMyLastGM() external view returns (string memory, uint256) {
        return (lastGMMessage[msg.sender], lastGMTime[msg.sender]);
    }

    // 総GM数確認
    function getTotalGMs() external view returns (uint256) {
        return totalGMs;
    }

    // ownerだけがメッセージ削除（テスト用）
    function clearUserGM(address user) external {
        require(msg.sender == owner, "Not owner");
        lastGMTime[user] = 0;
        lastGMMessage[user] = "";
    }
}
