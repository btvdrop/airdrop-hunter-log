// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BASE_Daily_Claimer {
    address public owner;
    mapping(address => uint256) public points;
    mapping(address => uint256) public lastClaim;

    uint256 public constant DAILY_AMOUNT = 100;  // 毎日100ポイント
    uint256 public constant COOLDOWN = 1 days;   // 24時間に1回

    event Claimed(address indexed user, uint256 amount);

    constructor() {
        owner = msg.sender;
    }

    // 毎日claim（クールダウンあり）
    function claim() external {
        require(block.timestamp >= lastClaim[msg.sender] + COOLDOWN, "Too soon");
        lastClaim[msg.sender] = block.timestamp;
        points[msg.sender] += DAILY_AMOUNT;
        emit Claimed(msg.sender, DAILY_AMOUNT);
    }

    // 自分のポイント確認（無料）
    function getMyPoints() external view returns (uint256) {
        return points[msg.sender];
    }

    // ownerだけが追加ポイント配布（テスト/イベント用）
    function addPoints(address user, uint256 amount) external {
        require(msg.sender == owner, "Not owner");
        points[user] += amount;
        emit Claimed(user, amount);
    }
}
