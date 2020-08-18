# sol-notes

// Browser IDE = https://remix.ethereum.org/

# uint = unsigned integer / positive integers 1, 2, 3 etc

## BASICS ##

# Contract
contract MyContract {
    // Add code
}
#


# State Variables - exist on the blockchain
int public myInt = 1; // Positive or negative int
uint public myUint = 1; // Positive int
uint256 public myUint256 = 1; //
uint8 public myUint8 = 1; //
#


# Local Variables - local to function
function getValue() public pure returns(uint){
    uint value = 1;
    return value;
}
#


# Strings
string public myString = "Hello, World!";
bytes32 public myBites32 = "Hello, World!";
#


# Address
address public myAddress = 0x5A0b54D5dc17e0AadC38d2db430a0D3E029c4c;
#


# Custom Data Structure - Model arbitrary data
struct MyStruct{
    uint myInt;
    string myString;
}

MyStruct public myStruct = MyStruct(1, "Hello, World!");
#


# Arrays
uint[] public uintArray = [1, 2, 3];
string[] public stringArray = ["apple", "banana"];
string[] public values;
uint[][] public arrray2D = [ [1, 2, 3], [4, 5, 6] ];

function addValue(string memory _value) public {
    values.push(_value)
}

function valueCount() public view returns(uint){
    return values.length;
}
#


# Mappings - Key value store - creates ID key for each value
# - mapping(key => value) myMapping;
mapping(uint => string) public names;

// Add names to mapping with constructor function
constructor(){
    names[1] = "Adam";
    names[2] = "Carl";
}

mapping(uint => Book) public books;

struct Book {
    string title;
    string author;
}

function addBook(uint _id, string memory _title, string memory _author) public {
    books[_id] = Book(_title, _author)
}

# Nested Mappings - Mapping that returns a mapping
# - mapping(key => mapping(key2 => value2)) public myMapping;
mapping(address => mapping(uint => Book)) public myBooks;

function addMyBook(uint _id, string memory _title, string memory _author) public {
    // Captures address of person calling the function
    myBooks[msg.sender][_id] = Book(_title, _author);
}
#


# Conditionals
function isEvenNumber(uint _number) public view returns(bool){
    if(_number % 2 == 0){
        return true;
    } else {
        return false;
    }
}

# Loops
uint[] public numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];

function countEvenNumbers() public view returns(uint){
    uint count = 0;

    for(uint i = 0; i < numbers.length; i++){
        if(isEvenNumber(numbers[i])){
            count ++;
        }
    }
    return count;
}
#


# Conditional 2
address public owner;

// Store ownership when deployed
constructor() public {
    owner = msg.sender;
}

// Determine address is owners address
function isOwner() public view returns(bool){
    return(msg.sender == owner)
}
#

## END BASIC FUNDAMENTALS ##


## SMART CONTRACT EXAMPLE ##
# - Ether - pay smart contracts
# - Modifiers
# - Visibility
# - Events
# - Enums

contract HotelRoom {

    enum Statuses { Vacant, Occupied };     // Set Statuses options
    Statuses currentStatus;
    event Occupy(address _occupant, uint _value);
    address payable public owner;

    constructor() public {                  // constructor function runs when smart contract is created (deployed to blockchain)
        owner = msg.sender;                 // Whoever deploys contract is the owner - and can recieve payments
        currentStatus = Statuses.Vacant     // Set default status
    }

    modifier onlyWhileVacant {
        require(currentStatus == Statuses.Vacant, "Currently occupied.");   // Check status, throw error
        _;
    }

    modifier costs(uint _amount){
        require(msg.value >= _amount, "Not enough ether provided);          // Check value, throw error
        _;
    }
    
    receive() external payable onlyWhileVacant costs(2 ether) {     // Create a function that is triggered from payment (example: Metamask)
        currentStatus = Statuses.Occupied       // Change Status to Occupied
        owner.transfer(msg.value);              // Pay owner ( to book hotel room )
        emit Occupy(msg.sender, msg.value);     // Subscribe to event
    }

}
##


## SMART CONTRACT INHERITANCE ##
# - Inheritance
# - Factories
# - Smart contract interactions

pragma solidity ^0.6.0;


contract Ownable {

    address owner;

    constructor() public {
        owner = msg.sender;
    }

    modifier onlyOwner(){
        require(msg.sender == owner, "must be owner");
        _;
    }
}

contract SecretVault {
    string secret;  // Not Public

    constructor(string memory _secret) public {
        secret = _secret;
    }

    function getSecret() public view onlyOwner returns(string memory){
        return secret;
    }
}

contract MyContract is Ownable {  // Inherit from Ownable contract

    address secretVault

    constructor(string memory _secret) public {
        SecretVault _secretVault = new SecretVault(_secret);
        secret = address(_secretVault);
        super;  // calls constructor of smart contract that this inherits from
    }
   
    function getSecret() public view onlyOwner returns(string memory){
        SecretVault = _secretVault = SecretVault(secretVault);
        return _secretVault.getSecret();
    }
}
##