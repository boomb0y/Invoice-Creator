#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(int argc, char *argv[])
{
  FILE *ord,*tax,*inv;
  if (argc==1)
  {

      ord = fopen("order.txt", "r");
      tax = fopen("tax_rate.txt", "r");
      inv = fopen("invoice.txt", "w");

  } else {
      ord = fopen(argv[1], "r");
      tax = fopen(argv[2], "r");
      inv = fopen(argv[3], "w");
  }

int bufferLength = 256;
char *row1[] = {"Product", "Price", "Quantity","State","Tax"};
fprintf(inv,"%-10s     %-10s %3s %10s %10s\n",row1[0],row1[1],row1[2],row1[3],row1[4]);
fprintf(inv,"---------------------------------------------------------------------------\n");
char bufford[bufferLength];
char bufftax[bufferLength];
char name[64], quantity[64], unitprice[64], state[64];
char statetax[64], abbrev[64], rate[64];
char c;
int i;
float subtotal = 0;
float total = 0;
float taxx = 0;
float totalTax = 0;
float truetax = 0;


while (fgets(bufford, bufferLength - 1, ord))
{
    strcpy(name, strtok(bufford , ","));
    strcpy(quantity, strtok(NULL, " , \n\t"));
    strcpy(unitprice , strtok(NULL, " , \n\t"));
    strcpy(state , strtok(NULL, " , \n\t"));
    strcpy(name, strtok(bufford , " "));
    subtotal = atof(quantity)*atof(unitprice);
    
      while (fgets(bufftax, bufferLength - 1, tax)) 
      {
        strcpy(statetax, strtok(bufftax , ","));
        strcpy(abbrev, strtok(NULL, ","));
        strcpy(rate , strtok(NULL, ","));
        
        if((strstr(state, abbrev)) != NULL) 
        {

          truetax = atof(rate)/100;
          taxx = subtotal * truetax;
          subtotal = subtotal+taxx;
          totalTax = totalTax + taxx;
          total = total + subtotal;
          fprintf(inv,"%-10s     %-7s     %-10s   %-6s     %-6s",name,unitprice,quantity,state,rate);
          fprintf(inv,"Subtotal:%.2f\n\n", subtotal);
          rewind(tax);
          break;
        }
        
      }

}
fprintf(inv,"Total Taxes:%.2f\nGrand Total:%.2f\n", totalTax,total);
printf("Complete!\n");
fclose(tax);
fclose(inv);
fclose(ord);
return 0;
}

