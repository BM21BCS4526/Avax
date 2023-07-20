** Deploying smart contract on Avalanche Network for Degen Gaming**
In this project, we are creating a smart contract for Degen gamin using solidity language and deploying it on the avalanche network.

**Description**
we have created an ERC20 token on the Avalanche network, allowing players to mint, transfer, redeem, check token balance, and burn their own tokens.
The smart contract has various functionality, including in-game store items redemption, checking balance, and burning tokens.

**Getting Started**
**Executing program**
This code can be compiled and deployed using Remix.
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Burnable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract DegenToken is ERC20, ERC20Burnable, Ownable {

    mapping(uint256 => uint256) private _itemPrices;
    mapping(address => mapping(uint256 => uint256)) private _playerItems;
   
    mapping(address => uint256) private _balances;
    uint256 private _totalSupply;

    event ItemRedeemed(address indexed player, uint256 itemId);

    constructor() ERC20("Degen Gaming Token", "DEGEN") {
        uint256 _initialSupply = 1000000 * 10**decimals();
         _totalSupply = _initialSupply;
        _balances[msg.sender] = _totalSupply;
        _mint(msg.sender, _initialSupply);

    
        _itemPrices[1] = 1000 * 10**decimals(); // Item 1 costs 1000 DEGEN tokens
        _itemPrices[2] = 500 * 10**decimals();  // Item 2 costs 500 DEGEN tokens
        
    }
     function balanceOf(address account) public view override returns (uint256) {
        return _balances[account];
    }
     function transfer(address recipient, uint256 amount) public override returns (bool) {
        _transfer(msg.sender, recipient, amount);
        return true;
    }
     function transferTokens(address _receiver, uint256 _value) external {
        require(balanceOf(msg.sender)>= _value, "you do not enough Degen Token");
        approve(msg.sender,_value);
        transferFrom(msg.sender,_receiver,_value);
    }
    function mint(address account, uint256 amount) public onlyOwner {
        _mint(account, amount);
    }
    function getBalance() external view returns (uint256)
    {
        return this.balanceOf(msg.sender);
    }
    function burnTokens(uint256 amount) public {
        _burn(msg.sender, amount);
        
     }
      // Function to get the price of an item in DEGEN tokens
    function getItemPrice(uint256 itemId) public view returns (uint256) {
        return _itemPrices[itemId];
    }

    // Function to redeem an in-game item using DEGEN tokens
    function redeem(uint256 itemId) public {
        uint256 itemPrice = getItemPrice(itemId);
        require(itemPrice > 0, "Invalid item ID");

        _burn(msg.sender, itemPrice);
        _playerItems[msg.sender][itemId] += 1;

        emit ItemRedeemed(msg.sender, itemId);
    }

    // Function to get the count of a specific item owned by a player
    function getPlayerItemCount(address player, uint256 itemId) public view returns (uint256) {
        return _playerItems[player][itemId];
    }
    
}
 
 Author
 Baby Monal
