// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BASE_Simple_Access_Vault {
    address public owner;
    address public manager;  // 追加の役割（Manager）

    mapping(address => uint256) public balances;

    event Deposited(address indexed user, uint256 amount);
    event Withdrawn(address indexed user, uint256 amount);
    event ManagerSet(address indexed newManager);

    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }

    modifier onlyOwnerOrManager() {
        require(msg.sender == owner || msg.sender == manager, "Not authorized");
        _;
    }

    constructor() {
        owner = msg.sender;
        manager = address(0);  // 最初は未設定
    }

    // 誰でもdeposit可能
    function deposit() external payable {
        require(msg.value > 0, "Deposit amount must be greater than 0");
        balances[msg.sender] += msg.value;
        emit Deposited(msg.sender, msg.value);
    }

    // withdrawはOwnerかManagerだけ可能（セキュリティ意識）
    function withdraw(uint256 amount) external onlyOwnerOrManager {
        require(balances[msg.sender] >= amount, "Insufficient balance");
        balances[msg.sender] -= amount;
        payable(msg.sender).transfer(amount);
        emit Withdrawn(msg.sender, amount);
    }

    // ManagerをOwnerだけが設定可能
    function setManager(address newManager) external onlyOwner {
        manager = newManager;
        emit ManagerSet(newManager);
    }

    // コントラクト総残高（誰でも見れる）
    function getTotalBalance() external view returns (uint256) {
        return address(this).balance;
    }

    // 自分の残高
    function getMyBalance() external view returns (uint256) {
        return balances[msg.sender];
    }

    // 緊急引き出し（Owner専用）
    function emergencyWithdraw() external onlyOwner {
        uint256 bal = address(this).balance;
        payable(owner).transfer(bal);
    }
}
