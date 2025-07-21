# FlagShop - Writeup
## Solve Time: ~7mins
## Challenge Description

<img width="957" height="682" alt="image" src="https://github.com/user-attachments/assets/b5214f83-baab-4ece-b44a-c2663678da10" />


## My Approach and Thought Process
- i ran the nc and i was prompted with a Shop like system and we also had a source code attached to the Challenge, which is obviously the code that the nc uses
  
<img width="563" height="282" alt="{5204104C-1683-48F9-8963-A9F032E70E57}" src="https://github.com/user-attachments/assets/909b6445-a3ca-436a-949d-c3ccb5337090" />

- Status: We have 1100 and there are 2 kinds of flags, fake flags and the real flag, the fake flag costs 900 and the real flag costs 10000000 which is way out of our budget, so we have to improvise.
- I tried buying fake flags using negative integers but it didnt work so i knew there was some kind of trick inside the source code

<img width="796" height="495" alt="{146ADBE8-4023-40D0-B0DD-86E8BEB686BA}" src="https://github.com/user-attachments/assets/1596afb6-9df3-42c8-9e7b-829b0f7a4f85" />



## Digging Deeper
- checking the source code, i find this snippet
``` 
if(auction_choice == 1){                                            
printf("These knockoff Flags cost 900 each, enter desired       
quantity\n");                                                                   
                                                                
int number_flags = 0;                                           
fflush(stdin);                                                  
scanf("%d", &number_flags);                                     
if(number_flags > 0){                                           
    int total_cost = 0;                                         
    total_cost = 900*number_flags;                              
    printf("\nThe final cost is: %d\n", total_cost);            
    if(total_cost <= account_balance){                          
        account_balance = account_balance - total_cost;         
        printf("\nYour current balance after transaction:       
%d\n\n", account_balance);                                                      
    }                                                           
    else{                                                       
        printf("Not enough funds to complete purchase\n");      
    }                                                           
                                                             
}
```

- It looks pretty normal, but this is infact an unsafe code because of the cost calculations
- `total_cost =900*number_flags`. total_cost is a variable that stores an integer, but it doesnt have any protection or sanitisation so in theory , if we could reach the integer limit, it would turn from buying to selling ( because above the integer limit would break it resulting in a very huge Negative number, which in return = huge Negative price = huge profit )


## My Solution

- Starting with the logic we found in the code, we want to make the total_cost reach above the integer limit which is (2,147,483,647) so we need to multiply 900 by a huge number of flags to reach that, or we could divide the integer limit by 900 to get ( 2386092.94111 and round it to 2386093), but we need to bypass the integer limit so we go even higher ( around   just in case).

<img width="778" height="286" alt="{7FBCCD11-5C49-4829-AF8B-500F4C8A481F}" src="https://github.com/user-attachments/assets/b95ab9e0-dd0a-44a1-bbd5-8681faf11406" />



- It Worked, now we have way more than 100000 and we can purchase the real flag (1337 flag)
<img width="802" height="426" alt="{5F95BABF-64B1-4F1C-B4B4-5DFA18493071}" src="https://github.com/user-attachments/assets/13b73b14-2512-495f-bdb7-ee93675e1cb4" />


- Hope you enjoyed this challenge and learned something valuable

## What i Learned

- Buffer OverFlow basics
- code analysis
- integers
## Tools Used
- NC ( to connect to the server used by the challenge)
- neovim / or any other IDE / strings or glow , any way to see the code
## Support Me
I hope you enjoyed this writeup! If you want me to keep uploading writeups often, please show your love to the page and star the repo,Thank you for your time. Your support keeps me motivated to share more solutions! 
