import java.util.*;

public class ProcessSchedulingSimulation {

    // Process class to store process information
    static class Process {
        int pid; // Process ID
        int arrivalTime; // Arrival time
        int burstTime; // Burst time (CPU time)
        int remainingTime; // Time remaining for the process
        int priority; // Priority of the process (for Priority Scheduling)

        // Constructor
        public Process(int pid, int arrivalTime, int burstTime, int priority) {
            this.pid = pid;
            this.arrivalTime = arrivalTime;
            this.burstTime = burstTime;
            this.remainingTime = burstTime;
            this.priority = priority;
        }
    }

    // FCFS Scheduling (First-Come, First-Served)
    static class FCFS {
        public void schedule(List<Process> processes) {
            processes.sort(Comparator.comparingInt(p -> p.arrivalTime));
            int currentTime = 0;

            for (Process process : processes) {
                int waitingTime = currentTime - process.arrivalTime;
                currentTime += process.burstTime;
                int turnAroundTime = currentTime - process.arrivalTime;
                System.out.println("FCFS - Process " + process.pid + " | Waiting Time: " + waitingTime + " | Turnaround Time: " + turnAroundTime);
            }
        }
    }

    // Round Robin Scheduling
    static class RoundRobin {
        private final int timeQuantum;

        public RoundRobin(int timeQuantum) {
            this.timeQuantum = timeQuantum;
        }

        public void schedule(List<Process> processes) {
            Queue<Process> queue = new LinkedList<>();
            queue.addAll(processes);
            int currentTime = 0;

            while (!queue.isEmpty()) {
                Process process = queue.poll();
                if (process.remainingTime > timeQuantum) {
                    process.remainingTime -= timeQuantum;
                    currentTime += timeQuantum;
                    queue.add(process);
                } else {
                    currentTime += process.remainingTime;
                    process.remainingTime = 0;
                    int waitingTime = currentTime - process.arrivalTime - process.burstTime;
                    int turnAroundTime = currentTime - process.arrivalTime;
                    System.out.println("RR - Process " + process.pid + " | Waiting Time: " + waitingTime + " | Turnaround Time: " + turnAroundTime);
                }
            }
        }
    }

    // Priority Scheduling
    static class PriorityScheduling {
        public void schedule(List<Process> processes) {
            processes.sort(Comparator.comparingInt(p -> p.priority));
            int currentTime = 0;

            for (Process process : processes) {
                int waitingTime = currentTime - process.arrivalTime;
                currentTime += process.burstTime;
                int turnAroundTime = currentTime - process.arrivalTime;
                System.out.println("Priority - Process " + process.pid + " | Waiting Time: " + waitingTime + " | Turnaround Time: " + turnAroundTime);
            }
        }
    }

    // Main method to simulate scheduling
    public static void main(String[] args) {
        List<Process> processes = new ArrayList<>();
        processes.add(new Process(1, 0, 10, 2)); // Process 1
        processes.add(new Process(2, 1, 5, 1));  // Process 2
        processes.add(new Process(3, 2, 8, 3));  // Process 3

        // FCFS Scheduling
        System.out.println("First-Come, First-Served (FCFS):");
        FCFS fcfs = new FCFS();
        fcfs.schedule(new ArrayList<>(processes));  // Clone the list to reset it for next scheduling

        // Reset processes for Round Robin and Priority Scheduling
        resetProcesses(processes);

        // Round Robin Scheduling
        System.out.println("\nRound Robin (RR):");
        RoundRobin rr = new RoundRobin(4); // Time Quantum = 4
        rr.schedule(new ArrayList<>(processes));  // Clone the list to reset it for next scheduling

        // Reset processes for Priority Scheduling
        resetProcesses(processes);

        // Priority Scheduling
        System.out.println("\nPriority Scheduling:");
        PriorityScheduling priorityScheduling = new PriorityScheduling();
        priorityScheduling.schedule(new ArrayList<>(processes));  // Clone the list to reset it for next scheduling
    }

    // Helper method to reset processes for each new algorithm
    private static void resetProcesses(List<Process> processes) {
        for (Process process : processes) {
            process.remainingTime = process.burstTime;
        }
    }
}

