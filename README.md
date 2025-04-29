#include <stdio.h>
#include <string.h>
#include <stdlib.h>

struct tenant {		//tenant structure to store tenant details
    char name[50];
    int room_no;
    long int mobile_no;
    char date_of_join[20];
    long int adv_amount;
    long int rent;
    long int amount;
    struct tenant* next;
};

struct boys_hostel {	//boys_hostel structure to store hostel details
    char name[50];
    int rooms[10];         // Rooms 101-303
    int room_status[10];   // 0 = available, 1 = occupied, 2 = fully occupied (2 tenants in room)
    long int rent;
    struct tenant* tenants;
};

int isTenantExists(struct boys_hostel* boy_hostel, const char* name, int room_no);

void displayhostelfacilities()  // Displays Hostel facilities is a function
{
    printf("\n----Hostel Facilities---\n");
    printf("1. High Speed Wi-Fi\n");
    printf("2. Laundry Service\n");
    printf("3. Lift service Available\n");
    printf("4. CCTV security and night guard\n");
    printf("5. Mess with vegetarian and non-vegetarian\n");
    printf("-------------------------------------\n");
}

void displayhostelrules()	//Display Hostel Rules
{
    printf("\n---Hostel Rules---\n");
    printf("1. No Smoking or Alcohol\n");
    printf("2. Noise level should be kept low after 9pm\n");
    printf("3. Rent must be paid by the 5th of every month\n");
    printf("-----------------------------------------------\n");
}

//Initialize tenant to the hostel and adding to linked list
void initializeTenant(struct boys_hostel* boy_hostel, const char* name, int room_no, long int mobile_no, const char* date_of_join, long int adv_amount,long int rent,long int total_amount1) {
    struct tenant* new_tenant = (struct tenant*)malloc(sizeof(struct tenant));
    
    strcpy(new_tenant->name, name);
    new_tenant->room_no = room_no;
    new_tenant->mobile_no = mobile_no;
    strcpy(new_tenant->date_of_join, date_of_join);
    new_tenant->adv_amount = adv_amount;
    new_tenant->rent = rent;
    new_tenant->amount=total_amount1;
    new_tenant->next = boy_hostel->tenants; 
    
    boy_hostel->tenants = new_tenant; //boy_hostel->tenant holds at the new tenant node
    
    for (int i = 0; i < 10; i++) {  //Mark room as occupied by changing room status
        if (boy_hostel->rooms[i] == room_no) {
            if (boy_hostel->room_status[i] < 2) {
                boy_hostel->room_status[i]++;
            }
            break;
        }
    }
}

void adddetails(struct boys_hostel* boy_hostel) {  //Add tenant details
    char name[50];
    char date_of_join[20];
    int room_no, found;
    long int mobile_no;
    found = 0;

    printf("\nRent=5000 + Registration fee=1500\nTotal=6500\nRegistration fee:1000 is refundable at the time of vacating\n"); //print rent and registration fee

    printf("Enter Name:\n"); //Request tenant details
    getchar();  // Consume leftover newline from previous input
    fgets(name, sizeof(name), stdin);    //to take name with spaces
    name[strcspn(name, "\n")] = '\0';  // Remove newline character

    printf("Enter Mobile Number\n");
    scanf("%li", &mobile_no);
    getchar();

    printf("Enter the Date of Joining\n");
    fgets(date_of_join, sizeof(date_of_join), stdin);
    date_of_join[strcspn(date_of_join, "\n")] = '\0';  // Remove newline character

    long int adv_amount1 = 0, rent1 = 0, total_amount1 = 0, rent, adv_amount;

    while (adv_amount1 < 1500) {   //collect advance amount
        printf("Pay Advance Amount for registration:\n");
        scanf("%li", &adv_amount);
        adv_amount1 += adv_amount;
        if (adv_amount1 < 1500) {
            printf("Remaining Advance amount=%li\n", 1500 - adv_amount1);
        }
    }

    do {  //collect rent and check if the amount is sufficient
        printf("Pay Rent to allocate room\n");
        scanf("%li", &rent);
        rent1 += rent;
        total_amount1 = rent1 + adv_amount1;

        if (total_amount1 < 6500) {  // 6500 is the total amount needed for the tenant (rent + advance)
            printf("Pay remaining Amount to allocate room.\n");
            printf("Remaining Amount: %li\n", 6500 - total_amount1);
            }
             printf("Total=%li\n", total_amount1);
    } while (total_amount1 < 6500);// Make sure total amount is at least 6500 (rent + registration)
            
    do {	//Room allocation loop
        printf("Enter room number (101-110):\n");
        scanf("%d", &room_no);

        if (room_no < 101 || room_no > 110) {
            printf("Invalid room number. Please enter a valid room number.\n");
            continue;
        }
       
        if (isTenantExists(boy_hostel, name, room_no)) {   //Check tenant located in the specific name
            printf("Error: Tenant %s is already allocated to Room %d.\n", name, room_no);
            return;  // Exit if tenant already exists
        }

        for (int i = 0; i < 10; i++) {  //If room is available allocate room
            if (boy_hostel->rooms[i] == room_no && boy_hostel->room_status[i] < 2) {  // Room has space for 2 tenants
                found = 1;
                break;
            }
        }

        if (found) {
            initializeTenant(boy_hostel, name, room_no, mobile_no, date_of_join, adv_amount1, rent1,total_amount1); //Allocate room to the tenant
            printf("Room %d allocated to %s in Boys Hostel\n", room_no, name);
        } else {
            printf("Room %d is unavailable or does not exist in Boys Hostel.\n", room_no);
        }

        if (!found) {
            char retry;
            printf("Do you want to try again? (y/n): ");
            scanf(" %c", &retry);
            if (retry == 'n' || retry == 'N') {
                break;
            }
        }
    } while (!found);

    printf("---------------------------------------------\n");
}

int isTenantExists(struct boys_hostel* boy_hostel, const char* name, int room_no) {
    struct tenant* temp = boy_hostel->tenants;
    while (temp != NULL) {
        if (strcmp(temp->name, name) == 0 && temp->room_no == room_no) {
            return 1;  // Tenant exists
        }
        temp = temp->next;
    }
    return 0;  // Tenant does not exist
}

void displayhosteldetails(char hostel_type[], char name[], int rooms[], int room_status[], long int rent) {
    printf("\nHostel Type: %s\n", hostel_type);
    printf("Hostel Name: %s\n", name);
    printf("Empty Rooms: ");
    for (int i = 0; i < 10; i++) {
        if (room_status[i] == 0) { // Show rooms that are available for at least one tenant
            printf("%d ", rooms[i]);
        }
    }
    printf("\nFilled Rooms: ");
    for(int i=0;i<10;i++){
	    if(room_status[i]>0){     //it will show the rooms filled atleast one or two persons in the room
		    printf("%d ",rooms[i]);
	    }
    }
    printf("\nRoom Filled With Single Tenant: ");
    for (int i = 0; i < 10; i++) {
        if (room_status[i] == 1) { // Show rooms that are filled with at least one tenant
            printf("%d ", rooms[i]);
        }
    }
    printf("\nFully Occupied Rooms With Two Tenant: ");
    for (int i = 0; i < 10; i++) {
        if (room_status[i] == 2) { // Show rooms that are fully occupied
            printf("%d ", rooms[i]);
        }
    }
    printf("\nMonthly Rent: %ld\n", rent);
    printf("-----------------------------------\n");
}

void displayAllTenants(struct boys_hostel* boy_hostel) {    //Display All Tenants
    struct tenant* temp = boy_hostel->tenants;
    if (temp == NULL) {
        printf("\nNo tenants found in the hostel.\n");
        return;
    }
    printf("\n----- All Tenants in Boys Hostel -----\n");
    while (temp != NULL) {
        printf("Name: %s, Room No: %d, \nMobile: %li, Date of Joining: %s, \nAdvance Amount: %liRent: %li\n",
               temp->name, temp->room_no, temp->mobile_no, temp->date_of_join, temp->adv_amount,temp->rent);
        temp = temp->next;
    }
    printf("-------------------------------------\n");
}

void displaytenants_by_room_no(struct boys_hostel* boy_hostel) {
    struct tenant* temp = boy_hostel->tenants;
    int room_no, found = 0;
    printf("\nEnter the Room No to display tenant details\n");
    scanf("%d", &room_no);
    printf("Tenant Details in Room No: %d\n", room_no);
    while (temp != NULL) {
        if (room_no == temp->room_no) {
            printf("Name: %s, Room no: %d\n", temp->name, temp->room_no);
            printf("Mobile Number: %li\n", temp->mobile_no);        //temp is a pointer* so we are using arrow-> opperator
            printf("Date of Joining: %s\n", temp->date_of_join);
            printf("Advance Amount: %li\n", temp->adv_amount);
	    printf("Rent:%li\n",temp->rent);
	    printf("----------------------------------------\n");
            found = 1;
        }
        temp = temp->next;
    }
    if (!found) {
        printf("No Tenants in the Room No: %d\n", room_no);
    }
}

void saveToFile(struct boys_hostel* boy_hostel) {  //Save to file hostel_data.txt
    FILE *file = fopen("hostel_data.txt", "w");
    if (!file) {
        printf("\nError opening file for saving.\n");
        return;
    }

    struct tenant* temp = boy_hostel->tenants;
    while (temp != NULL) {
        fprintf(file, "%s\n%d\n%li\n%s\n%li\n%li\n%li\n", 
                temp->name, temp->room_no, temp->mobile_no, 
                temp->date_of_join, temp->adv_amount, temp->rent, temp->amount);
        temp = temp->next;
    }
    fclose(file);
    printf("Data saved to file.\n");
}

void loadFromFile(struct boys_hostel* boy_hostel) {
    FILE *file = fopen("hostel_data.txt", "r");
    if (!file) {
        printf("\nError opening file for loading.\n");
        return;
    }

    char name[50], date_of_join[20];
    int room_no;
    long int mobile_no, adv_amount, rent, amount;
    while (fscanf(file, "%49[^\n]\n%d\n%li\n%19[^\n]\n%li\n%li\n%li\n", 
                   name, &room_no, &mobile_no, date_of_join, 
                   &adv_amount, &rent, &amount) == 7) {
        initializeTenant(boy_hostel, name, room_no, mobile_no, date_of_join, adv_amount, rent, amount);
    }
    fclose(file);
    printf("Data loaded from file.\n");
}

void freetenant(struct boys_hostel* boy_hostel) {
    int room_no;
    char tenant_name[50];
    printf("\nEnter Room No to free tenant:\n");
    scanf("%d", &room_no);
    getchar(); // Consume newline character from input buffer

    struct tenant* temp = boy_hostel->tenants;
    int tenant_found = 0;  // Flag to check if tenants are found
    printf("Tenants in Room No %d:\n", room_no);
    while (temp != NULL) {
        if (temp->room_no == room_no) {
            printf("- %s\n", temp->name); //Display tenant names in the specific room
            tenant_found = 1;
        }
        temp = temp->next;
    }
    if (!tenant_found) {
        printf("No tenants found in Room No %d.\n", room_no);
        return;
    }

    printf("Enter the name of the tenant to free:\n");
    fgets(tenant_name, sizeof(tenant_name), stdin);
    tenant_name[strcspn(tenant_name, "\n")] = '\0';  // Remove newline character
    temp = boy_hostel->tenants;
    struct tenant* prev = NULL;

    while (temp != NULL) {  //Traverse linked lint to find the tenant
        if (temp->room_no == room_no && strcmp(temp->name, tenant_name) == 0) {

            printf("Are you sure you want to free %s from Room No %d? (y/n): ", tenant_name, room_no); //comfirm tenant
            char confirm;
            scanf(" %c", &confirm);
            if (confirm == 'y' || confirm == 'Y') {

                if (prev == NULL) {
                    boy_hostel->tenants = temp->next;  // Removing the head
                } else {
                    prev->next = temp->next;  // Removing a middle or end node
                }

                for (int i = 0; i < 10; i++) {  //Room status
                    if (boy_hostel->rooms[i] == room_no) {
                        boy_hostel->room_status[i]--;  // Reduce occupancy
                        break;
                    }
                }
                printf("Tenant %s has been freed from Room No %d.\n", tenant_name, room_no);
                free(temp);  // Free the memory
                return;      // Exit after freeing the tenant
            } else {
                printf("Tenant not freed.\n");
                return;
            }
        }
        prev = temp;
        temp = temp->next;
    }
    printf("No tenant with the name %s found in Room No %d.\n", tenant_name, room_no);
}

void freeAllTenants(struct boys_hostel* boy_hostel) {  //Function to free all tenants
    struct tenant* temp = boy_hostel->tenants;
    struct tenant* next_tenant;

    while (temp != NULL) {
        next_tenant = temp->next;  // Save the next tenant reference
        printf("\nFreeing tenant: %s from Room No %d\n", temp->name, temp->room_no);
       
        for (int i = 0; i < 10; i++) {
            if (boy_hostel->rooms[i] == temp->room_no) {
                boy_hostel->room_status[i] = 0;  // update room status to 0
                break;
            }
        }
        free(temp);  // Free the memory allocated for the tenant
        temp = next_tenant;  // Move to the next tenant
    }
    boy_hostel->tenants = NULL; //After freeing all tenants set tenants to NULL
    printf("All tenants have been freed and rooms are now available.\n");
}

int main() {
    struct boys_hostel boy_hostel = {      //boy_hostel is a object   //boys_hostel is a structure name
        .name = "Nilakanta Boys Hostel",    // . operator in structure
        .rooms = {101, 102, 103, 104, 105, 106, 107, 108, 109, 110},
        .room_status = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0},
        .rent = 5000,
        .tenants = NULL
    };
    loadFromFile(&boy_hostel); //Load data
    int choice;
    do {
        printf("\nWelcome to the Nilakanta Boys Hostel Management System\n");
        printf("1. Display Hostel facilities\n");
        printf("2. Display Hostel Rules\n");
        printf("3. Display Hostel Details\n");
        printf("4. Display Tenants by Room\n");
        printf("5. Display All Tenants\n");
        printf("6. Add Tenant Details\n");
        printf("7. Free Specific Tenant\n");
        printf("8. Free all Tenants\n");
        printf("9. Save and Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {    //function callings
            case 1: displayhostelfacilities(); break;
            case 2: displayhostelrules(); break;
            case 3: displayhosteldetails("Boys Hostel", boy_hostel.name, boy_hostel.rooms, boy_hostel.room_status, boy_hostel.rent); break;
            case 4: displaytenants_by_room_no(&boy_hostel); break;
            case 5: displayAllTenants(&boy_hostel); break;
            case 6: adddetails(&boy_hostel); break;
            case 7: freetenant(&boy_hostel); break;
            case 8: freeAllTenants(&boy_hostel); break;
            case 9: saveToFile(&boy_hostel);
            printf("Thank you for choosing this Pg! Have a nice day!.\n"); break;
            default: printf("Invalid choice. Please try again.\n");
        }
    } while (choice != 9);
    return 0;
}
