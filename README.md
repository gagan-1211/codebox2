#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define CAR 1
#define SCOOTER 2

struct vehicle
{
    char num[20];
    int row;
    int col;
    int type;
};

int parkinfo[4][10];
int vehcount;
int carcount;
int scootercount;

void display();
struct vehicle* add(int, const char*, int, int);
void del(struct vehicle*);
void displayFreeSpace();
void getfreerowcol(int, int*);
void getrcbyinfo(int, const char*, int*);

struct vehicle* add(int t, const char* num, int row, int col)
{
    if (vehcount >= 40)
    {
        printf("\nParking is full. Cannot add more vehicles.");
        return NULL;
    }

    struct vehicle* v = (struct vehicle*)malloc(sizeof(struct vehicle));

    v->type = t;
    strcpy(v->num, num);
    v->row = row;
    v->col = col;

    if (t == CAR)
        carcount++;
    else
        scootercount++;

    vehcount++;
    strcpy(parkinfo[row][col], num);

    return v;
}

void del(struct vehicle* v)
{
    if (v == NULL)
    {
        printf("\nInvalid vehicle. Cannot delete.");
        return;
    }

    int c;

    for (c = v->col; c < 9; c++)
        strcpy(parkinfo[v->row][c], parkinfo[v->row][c + 1]);

    strcpy(parkinfo[v->row][c], "");

    if (v->type == CAR)
        carcount--;
    else
        scootercount--;

    vehcount--;

    free(v);
}

void getfreerowcol(int type, int* arr)
{
    int r, c, fromrow = 0, torow = 2;

    if (type == SCOOTER)
    {
        fromrow += 2;
        torow += 2;
    }

    for (r = fromrow; r < torow; r++)
    {
        for (c = 0; c < 10; c++)
        {
            if (parkinfo[r][c] == 0)
            {
                arr[0] = r;
                arr[1] = c;
                return;
            }
        }
    }

    arr[0] = -1;
    arr[1] = -1;
}

void getrcbyinfo(int type, const char* num, int* arr)
{
    int r, c, fromrow = 0, torow = 2;

    if (type == SCOOTER)
    {
        fromrow += 2;
        torow += 2;
    }

    for (r = fromrow; r < torow; r++)
    {
        for (c = 0; c < 10; c++)
        {
            if (strcmp(parkinfo[r][c], num) == 0)
            {
                arr[0] = r;
                arr[1] = c;
                return;
            }
        }
    }

    arr[0] = -1;
    arr[1] = -1;
}

void display()
{
    int r, c, n;

    for (r = 0; r < 4; r++)
    {
        for (c = 0; c < 10; c++)
        {
            n = parkinfo[r][c];
            if (n == 0)
                printf(" __ ");
            else
                printf("%3s ", parkinfo[r][c]);
        }
        printf("\n");
    }
}

int main()
{
    int ch, type, row, col;
    char info[20]; // Store vehicle numbers as strings
    struct vehicle* v;

    vehcount = 0;
    carcount = 0;
    scootercount = 0
   
    while (1)
    {
        system("cls");
        displayFreeSpace();
        printf("\n1. Add Vehicle");
        printf("\n2. Delete Vehicle");
        printf("\n3. Display Parking Lot");
        printf("\n4. Exit");
        printf("\nEnter your choice: ");
        scanf("%d", &ch);

        switch (ch)
        {
        case 1:
            printf("\nType of vehicle (1 - Car, 2 - Scooter): ");
            scanf("%d", &type);

            if (type != CAR && type != SCOOTER)
            {
                printf("Invalid vehicle type.\n");
                getchar();
                break;
            }

            printf("Enter vehicle number: ");
            scanf("%s", info);

            if (strlen(info) >= sizeof(info))
            {
                printf("Invalid vehicle number.\n");
                getchar();
                break;
            }

            getfreerowcol(type, rc);
            row = rc[0];
            col = rc[1];

            if (row == -1)
            {
                printf("\nNo space available for parking this type of vehicle.\n");
                getchar();
                break;
            }

            v = add(type, info, row, col);

            if (v)
            {
                printf("\nVehicle parked successfully at row %d, col %d.\n", row + 1, col + 1);
            }
            else
            {
                printf("\nFailed to park the vehicle.\n");
            }

            printf("Press any key to continue...");
            getchar();
            getchar();
            break;

        case 2:
            printf("\nEnter vehicle number to be departed: ");
            scanf("%s", info);

            getrcbyinfo(CAR, info, rc);
            if (rc[0] == -1)
                getrcbyinfo(SCOOTER, info, rc);

            row = rc[0];
            col = rc[1];

            if (row == -1)
            {
                printf("\nVehicle with number %s not found.\n", info);
                getchar();
                getchar();
                break;
            }

            del(v);

            printf("\nVehicle with number %s departed successfully.\n", info);
            printf("Press any key to continue...");
            getchar();
            getchar();
            break;

        case 3:
            display();
            printf("\nTotal Vehicles Parked: %d (Cars: %d, Scooters: %d)\n", vehcount, carcount, scootercount);
            printf("Press any key to continue...");
            getchar();
            getchar();
            break;

        case 4:
            exit(0);

        default:
            printf("\nInvalid choice. Press any key to continue...\n");
            getchar();
            getchar();
            break;
        }
    }

    return 0;
}
