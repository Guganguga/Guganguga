import random
import time
from collections import deque

# Call class to represent an incoming call
class Call:
    def __init__(self, caller_id):
        self.caller_id = caller_id
        self.wait_time = 0

    def increment_wait_time(self):
        self.wait_time += 1

# Agent class to represent a call center agent
class Agent:
    def __init__(self, name):
        self.name = name
        self.current_call = None

    def take_call(self, call):
        self.current_call = call
        print(f"{self.name} is handling call from {call.caller_id}")

    def finish_call(self):
        print(f"{self.name} finished call from {self.current_call.caller_id}")
        self.current_call = None

# CallCenter class to manage calls and agents
class CallCenter:
    def __init__(self, num_agents):
        self.agents = [Agent(f"Agent {i + 1}") for i in range(num_agents)]
        self.call_queue = deque()
        self.available_agents = self.agents.copy()

    def receive_call(self, caller_id):
        new_call = Call(caller_id)
        self.call_queue.append(new_call)
        print(f"Received call from {caller_id}")

    def assign_calls(self):
        while self.call_queue and self.available_agents:
            call = self.call_queue.popleft()
            agent = self.available_agents.pop(0)
            agent.take_call(call)

    def release_agent(self, agent):
        if agent.current_call:
            agent.finish_call()
        self.available_agents.append(agent)

    def process_calls(self):
        while self.call_queue or any(agent.current_call for agent in self.agents):
            self.assign_calls()
            for agent in self.agents:
                if agent.current_call:
                    agent.current_call.increment_wait_time()
                    if random.random() < 0.1:  # Simulate call completion
                        self.release_agent(agent)
            time.sleep(1)  # Simulate time passing

# Main function to simulate the call center
def main():
    call_center = CallCenter(num_agents=3)

    # Simulate incoming calls
    for i in range(10):
        call_center.receive_call(f"Caller {i + 1}")
        time.sleep(random.uniform(0.5, 1.5))  # Random interval for new calls

    call_center.process_calls()

if __name__ == "__main__":
    main()

