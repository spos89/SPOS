import java.io.*;
import java.util.*;

public class macroPass1 {
    public static void main(String[] args) throws IOException {
        BufferedReader b1 = new BufferedReader(new FileReader("input.txt"));
        FileWriter f1 = new FileWriter("intermediate.txt");
        FileWriter f2 = new FileWriter("mnt.txt");
        FileWriter f3 = new FileWriter("mdt.txt");
        FileWriter f4 = new FileWriter("ala.txt");

        HashMap<String, Integer> pntab = new HashMap<>();
        String s;
        int paramNo = 1, mdtp = 1;
        int state = 0;
        int pp = 0, kp = 0, kpdtp = 0;

        while ((s = b1.readLine()) != null) {
            if (s.trim().isEmpty()) continue;
            String word[] = s.trim().split("\\s+");
            if (word[0].equalsIgnoreCase("MACRO")) {
                state = 1;
                continue;
            }
            if (state == 1 && !word[0].equalsIgnoreCase("MEND")) {
                String macroName = word[0];
                String params[] = {};
                if (word.length > 1) params = word[1].split(",");
                paramNo = 1;
                pp = 0;
                kp = 0;
                for (int i = 0; i < params.length; i++) {
                    String p = params[i].trim();
                    if (p.startsWith("&")) {
                        if (p.contains("=")) {
                            kp++;
                            String keywordParam[] = p.split("=", 2);
                            pntab.put(keywordParam[0].substring(1), paramNo++);
                            if (keywordParam.length == 2 && !keywordParam[1].isEmpty()) {
                                f4.write(keywordParam[0].substring(1) + "\t" + keywordParam[1] + "\n");
                            } else {
                                f4.write(keywordParam[0].substring(1) + "\t" + "-" + "\n");
                            }
                        } else {
                            pp++;
                            pntab.put(p.substring(1), paramNo++);
                        }
                    }
                }
                f2.write(macroName + "\t" + pp + "\t" + kp + "\t" + mdtp + "\t" +
                        (kp == 0 ? kpdtp : (kpdtp + 1)) + "\n");
                kpdtp += kp;
                state = 2;
                continue;
            }
            if (word[0].equalsIgnoreCase("MEND") && state == 2) {
                f3.write("MEND\n");
                state = 0;
                pp = kp = 0;
                mdtp++;
                paramNo = 1;
                pntab.clear();
            } else if (state == 2) {
                for (int i = 0; i < s.length(); i++) {
                    if (s.charAt(i) == '&') {
                        i++;
                        StringBuilder temp = new StringBuilder();
                        while (i < s.length() && s.charAt(i) != ' ' && s.charAt(i) != ',') {
                            temp.append(s.charAt(i++));
                        }
                        i--;
                        String key = temp.toString();
                        if (pntab.containsKey(key)) {
                            f3.write("#" + pntab.get(key));
                        } else {
                            f3.write("&" + key);
                        }
                    } else {
                        f3.write(s.charAt(i));
                    }
                }
                f3.write("\n");
                mdtp++;
            } else {
                f1.write(s + "\n");
            }
        }

        b1.close();
        f1.close();
        f2.close();
        f3.close();
        f4.close();

        printFile("mnt.txt", "Macro Name Table (MNT):");
        printFile("mdt.txt", "Macro Definition Table (MDT):");
        printFile("ala.txt", "Argument List Array (ALA):");
        printFile("intermediate.txt", "Intermediate Code:");
    }

    public static void printFile(String filename, String title) throws IOException {
        System.out.println("\n--- " + title + " ---");
        BufferedReader br = new BufferedReader(new FileReader(filename));
        String line;
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }
        br.close();
    }
}
