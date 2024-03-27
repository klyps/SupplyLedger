# Blockchain-based Supply Chain Finance
Leverage blockchain technology to streamline supply chain finance, reducing costs and increasing access to capital for small and medium-sized enterprises.
import hashlib
import time

class Block:
    def __init__(self, index, transactions, timestamp, previous_hash):
        self.index = index
        self.transactions = transactions
        self.timestamp = timestamp
        self.previous_hash = previous_hash
        self.nonce = 0
        self.hash = self.calculate_hash()

    def calculate_hash(self):
        block_string = "{}{}{}{}{}".format(self.index, self.transactions, self.timestamp, self.previous_hash, self.nonce)
        return hashlib.sha256(block_string.encode()).hexdigest()

    def mine_block(self, difficulty):
        while self.hash[:difficulty] != '0' * difficulty:
            self.nonce += 1
            self.hash = self.calculate_hash()

class Blockchain:
    def __init__(self):
        self.chain = [self.create_genesis_block()]
        self.pending_transactions = []
        self.difficulty = 2
        self.mining_reward = 1

    def create_genesis_block(self):
        return Block(0, "Genesis Block", time.time(), "0")

    def get_last_block(self):
        return self.chain[-1]

    def add_transaction(self, transaction):
        self.pending_transactions.append(transaction)

    def mine_pending_transactions(self, miner_address):
        block = Block(len(self.chain), self.pending_transactions, time.time(), self.get_last_block().hash)
        block.mine_block(self.difficulty)

        print(f"Block successfully mined by {miner_address}! Hash: {block.hash}")
        self.chain.append(block)

        self.pending_transactions = [
            {"from_address": None, "to_address": miner_address, "amount": self.mining_reward}
        ]

    def is_chain_valid(self):
        for i in range(1, len(self.chain)):
            current_block = self.chain[i]
            previous_block = self.chain[i-1]

            if current_block.hash != current_block.calculate_hash():
                print("Current Block's hash is incorrect")
                return False

            if current_block.previous_hash != previous_block.hash:
                print("Previous Block's hash doesn't match")
                return False

        return True

# Example usage
blockchain = Blockchain()
blockchain.add_transaction({"from_address": "companyA", "to_address": "supplierB", "amount": 100})
blockchain.mine_pending_transactions("miner1")

print("Blockchain valid?", blockchain.is_chain_valid())

# Inspect the blockchain
for block in blockchain.chain:
    print("Block Hash:", block.hash)
