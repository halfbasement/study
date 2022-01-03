# Scanner

## next() vs nextLine()
- next는 공백(스페이스바) 전까지만 입력 (Hello world->Hello)
- nextLine은 공백 포함 전부 입력 (Hello world->Hello)


### 2924
```java
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int x = sc.nextInt();
        int y = sc.nextInt();

        int[] month ={31,28,31,30,31,30,31,31,30,31,30,31};
        String[] day={"SUN","MON","TUE","WED","THU","FRI","SAT"};

        int sum=0;

        for(int i=1; i<x;i++){ 
            // i에 0 넣고 있었다 (ex:해당 입력월은 입력 안하고 일수로 환산해야하는데 월까지 더해버림)
            sum +=month[i-1];
        }

        y = (y+sum)%7;


       switch (y){
           case 0:
               System.out.println(day[0]);
               break;
           case 1:
               System.out.println(day[1]);
               break;
           case 2:
               System.out.println(day[2]);
               break;
           case 3:
               System.out.println(day[3]);
               break;
           case 4:
               System.out.println(day[4]);
               break;
           case 5:
               System.out.println(day[5]);
               break;
           case 6:
               System.out.println(day[6]);
               break;

       }

    }
}

```