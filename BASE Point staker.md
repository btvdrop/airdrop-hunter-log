// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BASE_Point_Staker {
    address public owner;
    uint256 public totalStaked;
    uint256 public rewardRate = 10; // 1ポイントあたり1日10報酬

    mapping(address => uint256) public stakedPoints;
    mapping(address => uint256) public lastUpdateTime;
    mapping(address => uint256) public rewards;

    event Staked(address indexed user, uint256 amount);
    event Unstaked(address indexed user, uint256 amount);
    event RewardClaimed(address indexed user, uint256 amount);

    constructor() {
        owner = msg.sender;
    }

    modifier updateReward(address account) {
        rewards[account] += (stakedPoints[account] * rewardRate * (block.timestamp - lastUpdateTime[account])) / 1 days;
        lastUpdateTime[account] = block.timestamp;
        _;
    }

    function stake(uint256 amount) external updateReward(msg.sender) {
        require(amount > 0, "Amount must be greater than 0");
        stakedPoints[msg.sender] += amount;
        totalStaked += amount;
        emit Staked(msg.sender, amount);
    }

    function unstake(uint256 amount) external updateReward(msg.sender) {
        require(stakedPoints[msg.sender] >= amount, "Insufficient staked");
        stakedPoints[msg.sender] -= amount;
        totalStaked -= amount;
        emit Unstaked(msg.sender, amount);
    }

    function claimRewards() external updateReward(msg.sender) {
        uint256 reward = rewards[msg.sender];
        require(reward > 0, "No rewards");
        rewards[msg.sender] = 0;
        // ここに報酬送金ロジック（テスト用にイベントだけ）
        emit RewardClaimed(msg.sender, reward);
    }

    function getPendingRewards(address user) external view returns (uint256) {
        return rewards[user] + (stakedPoints[user] * rewardRate * (block.timestamp - lastUpdateTime[user])) / 1 days;
    }
}
