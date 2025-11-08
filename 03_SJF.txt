import java.util.Scanner;

public class SJF {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.println("* Shortest Job First Scheduling (Preemptive) *");
        System.out.print("Enter number of processes: ");
        int n = sc.nextInt();

        int[] process = new int[n];
        int[] arrivalTime = new int[n];
        int[] burstTime = new int[n];
        int[] completionTime = new int[n];
        int[] turnaroundTime = new int[n];
        int[] waitingTime = new int[n];
        int[] remainingTime = new int[n];
        boolean[] isCompleted = new boolean[n];

        for (int i = 0; i < n; i++) {
            process[i] = i + 1;
            System.out.print("Enter Arrival Time for Process " + process[i] + ": ");
            arrivalTime[i] = sc.nextInt();
            System.out.print("Enter Burst Time for Process " + process[i] + ": ");
            burstTime[i] = sc.nextInt();
            remainingTime[i] = burstTime[i];
            isCompleted[i] = false;
        }

        int completed = 0, currentTime = 0;
        float totalWaitingTime = 0, totalTurnaroundTime = 0;

        while (completed != n) {
            int idx = -1;
            int minRemainingTime = Integer.MAX_VALUE;

            for (int i = 0; i < n; i++) {
                if (arrivalTime[i] <= currentTime && !isCompleted[i] && remainingTime[i] < minRemainingTime && remainingTime[i] > 0) {
                    minRemainingTime = remainingTime[i];
                    idx = i;
                }
            }

            if (idx != -1) {
                remainingTime[idx]--;
                currentTime++;

                if (remainingTime[idx] == 0) {
                    completionTime[idx] = currentTime;
                    turnaroundTime[idx] = completionTime[idx] - arrivalTime[idx];
                    waitingTime[idx] = turnaroundTime[idx] - burstTime[idx];

                    totalWaitingTime += waitingTime[idx];
                    totalTurnaroundTime += turnaroundTime[idx];
                    isCompleted[idx] = true;
                    completed++;
                }
            } else {
                currentTime++;
            }
        }

        System.out.println("\nProcess\tArrival\tBurst\tCompletion\tTurnaround\tWaiting");
        for (int i = 0; i < n; i++) {
            System.out.println("P" + process[i] + "\t" + arrivalTime[i] + "\t" + burstTime[i] + "\t" + completionTime[i] + "\t\t" + turnaroundTime[i] + "\t\t" + waitingTime[i]);
        }

        System.out.printf("\nAverage Turnaround Time: %.2f\n", totalTurnaroundTime / n);
        System.out.printf("Average Waiting Time: %.2f\n", totalWaitingTime / n);
        sc.close();
    }
}
