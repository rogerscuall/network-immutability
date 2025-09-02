# Network Immutability

This project was created to support my post in in [here]().
This project is extremely limitted as support material for this post and it does not in anyway try to represent a correct configuration scenario.

## How to use

1. Clone the repository
1. Create a virtual environment:
   ```bash
   python -m venv venv
   source venv/bin/activate
   ```
2. Install the required packages:

   ```bash
   pip install -r requirements.txt
   ansible-galaxy collection install -r requirements.yml
   ```
3. Run the example playbook:

   ```bash
   ansible-playbook playbook.yml -i inventory.yml
   ```

