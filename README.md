# dispatcher
/*
 * Dispatcher_original_code.c
 *
 *  Created on: 2018 M10 7
 *      Author: liux8275
 */

//////question: fgets(15 means what)  how to fgets and strtok and put in char array
/*
 * dispatcher.c
 *
 * Student ID: 153182750
 * Author    : Liang Liu
 */
# include <stdio.h>
# include <stdlib.h>
# include <string.h>

typedef struct Process{
	int PID;
	int running_time;
	int waiting_time;
	int block_time;
	int current_time;
	int start_block;
	int start_running;
	int start_ready;
}Process;

typedef struct Node{
	struct Process* data;
	struct Node* next;
	struct Node* pre;

}Node;

typedef struct Queue{
	struct Node* front;
	struct Node* rear;
	//int count;
}Queue;

Node* create_Node(){
	Node* n = (Node*)malloc(sizeof(Node));
	n->data = NULL;
	n->next = NULL;
	n->pre = NULL;
	return n;
}
Process* create_Process(){
	Process* p = (Process*)malloc(sizeof(Process));
	p->PID = 0;
	p->block_time = 0;
	//p->create_time = 0;
	//p->event = NULL;
	p->running_time = 0;
	p->waiting_time = 0;
	p->start_running = 0;
	p->start_block = 0;
	p->current_time = 0;
	p->start_ready = 0;
	return p;
}

Queue* create_queue(){
	Queue* q = (Queue*)malloc(sizeof(Queue));
	q->front = NULL;
	q->rear = NULL;
	//q->count = 0;
	return q;
}
int is_empty_queue(Queue* p){
	if (p->front == NULL){
		return 0;
	}
	return 1;
}
void enQueue(Queue* q, Node* p){///que shao pre!!!!!
	if (is_empty_queue(q) == 0){
		q->rear = p;
		q->front = p;
		p->next = NULL;
		//printf("was empty!");
	}
	else{
		q->rear->next = p;
		q->rear = p;
		p->next = NULL;
		//printf("was not  empty!");
	}
}

Node* deQueue(Queue* q){// que sao pre!!!!!!!!
	Node* n = q->front;
	q->front = q->front->next;
	return n;
}

Node* block_front = NULL;
Node* block_rear = NULL;
int main(int argc, char* argv[]){
	setbuf(stdout, NULL);
	Queue* running_queue = create_queue();
	Queue* ready_queue = create_queue();
	Queue* exist_queue = create_queue();

	 //------------------------test 1----------------------------------------------------
	int length = 20;
	int all_time [50] = {100,200,250,300,400,500,1000,1050,1100,1150,2000,2100,2200,2300,3000,3050,3450,3500,4000,5000};
	char* all_events [50] ={"C","C","R","C","T","R","C","T","I","I","E","R","E","R","I","T","T","E","I","E"};
	int process_id[50] = {1,2,1,3,0,3,4,0,3,1,4,2,3,1,1,0,0,1,2,2};// 0 means doesn't change anything in the code
	//----------------------------------------------------------------------------------


	/*
	//------------------------test 2----------------------------------------------------
	int length = 12;
	int all_time [50] = {100,200,500,600,700,800,1500,1600,1700,1800,3500,4500};
	char* all_events [50] ={"C","C","T","T","T","T","T","T","T","T","E","E"};
	int process_id[50] = {1,2,0,0,0,0,0,0,0,0,1,2};

	//----------------------------------------------------------------------------------
	 */

	/*
	 //------------------------test 4----------------------------------------------------
		int length = 6;
		int all_time [50] = {100,200,300,400,500,600};
		char* all_events [50] ={"C","C","C","E","E","E"};
		int process_id[50] = {1,2,3,1,2,3};
	//----------------------------------------------------------------------------------
 	 */

	/*
	//------------------------test 5----------------------------------------------------
	int length = 4;
	int all_time [100] = {100,200,1200,2000};
	char* all_events [50] ={"C","R","I","E"};
	int process_id[100] = {1,1,1,1};
	//----------------------------------------------------------------------------------
	*/

	/*
	//------------------------test 6----------------------------------------------------
	int length = 10;
	int all_time [50] = {100,500,600,700,800,1500,1600,1700,1800,3500};
	char* all_events [50] ={"C","T","T","T","T","T","T","T","T","E"};
	int process_id[50] = {1,0,0,0,0,0,0,0,0,1};
	//----------------------------------------------------------------------------------
	 */

	/*
	 //------------------------test 7----------------------------------------------------
	int length = 8;
	int all_time [50] = {100,250,550,750,850,950,1050,1500};
	char* all_events [50] ={"C","R","I","R","I","R","I","E"};
	int process_id[50] = {1,1,1,1,1,1,1,1};
	//----------------------------------------------------------------------------------
	*/

	/*
	//------------------------test 8----------------------------------------------------
	int length = 2;
	int all_time [50] = {100,2000};
	char* all_events [50] ={"C","E"};
	int process_id[50] = {1,1};
	//----------------------------------------------------------------------------------
	 * */

	Node* temp = create_Node();
	//int i = 0;
	int j;
	//char input[100];


	int total_running_time = 0;
	//printf("My eclipse can not print out the output for some reason but can print out on my friend laptop. So my code might be not able to run properly. I'm so sorry to bring this inconvenience to you. I'll send a email to Zima about this situation. If there's a problem it should be in all_events = strtok part. My apologize again!");
/*
	fgets(input, 50, stdin);

	while (strcmp(input, "\n") != 0){
		input[strlen(input)-1] = '\0'; //not sure if need
		all_time[i] = atoi(strtok(input, " "));
		all_events[i] = strtok(NULL, " ");
		printf("events: %s\n", all_events[i]);
		process_id[i] = atoi(strtok(NULL, " "));
		if (strtok(NULL, " ") != NULL){
			process_id[i] = atoi(strtok(NULL, " "));
		}
		i++;
		//fflush(stdin); not sure if need
		fgets(input, 50, stdin); //dont know if need it

	}
	printf("events: %s\n", &all_events);
	printf("----------------------\n");
//////////////////////
 * */
	int number_process = 0 ;
	int a;
	for (a=0;a<length;a++){
		printf("time: %d\n", all_time[a]);
		printf("events: %s\n", all_events[a]);
		printf("id: %d\n", process_id[a]);
	}
	printf("----------------------\n");
	/////////////////////////

	// now I have three array store time, event and id
	for (j=0;j<length;j++){
		Process* p = create_Process();
		Node* n = create_Node();
//		printf("time: %d\n", all_time[j]);
//		printf("events: %s\n", all_events[j]);
//		printf("id: %d\n", process_id[j]);
		if (strcmp(all_events[j], "C") == 0){
			printf("CCCCCCCCCCCCCCCCChello\n");
			number_process ++;
			p->PID = process_id[j];
			n->data = p;
			p->current_time = all_time[j];/////
			if (is_empty_queue(running_queue) == 0){ // if there's no node in running queue, then add in
				n->data->start_running = all_time[j];////////bu que ding
				printf("when meet C start_running time: %d  \n", all_time[j]);
				enQueue(running_queue,n);
				printf("running queue add success\n");//////
			}
			else{
				p->start_ready = all_time[j];
				enQueue(ready_queue, n);    // if there's node in running queue,then add in ready queue
				printf("ready queue add success\n");
			}

			printf("--------------out C----------------\n");
		}

		if (strcmp(all_events[j], "R") == 0){
			printf("RRRRRRRRRRRRRRRRhello\n");
			temp = deQueue(running_queue);//first need to update the running time of the process in running queue
			temp->data->current_time = all_time[j];///////
			temp->data->start_block = all_time[j];
			temp->data->running_time = temp->data->running_time + (all_time[j] - temp->data->start_running);
			printf("RRRwhen meet R total running time: %d  \n", temp->data->running_time);

			if (block_rear == NULL){// que shao pre
				block_front = temp;
				block_rear = temp;
				temp->pre = NULL;
				temp->next = NULL;
			}
			else{
				temp->pre = block_rear;
				block_rear->next = temp;
				block_rear = temp;
				temp->next = NULL;
			}
			printf("block front: %d", block_front->data->PID);
			printf("RRRisEmpty in ready: %d    \n ",is_empty_queue(ready_queue));////
			if (is_empty_queue(ready_queue) == 1){
				temp = deQueue(ready_queue);// then need to update the waiting time of the process moved from waiting to running
				temp->data->current_time = all_time[j];///////
				temp->data->start_running = all_time[j];
				temp->data->waiting_time = temp->data->waiting_time + (all_time[j] - temp->data->start_ready);

				enQueue(running_queue,temp);
			}

			printf("pid: %d, running time: %d, ready time: %d, block time: %d \n", temp->data->PID, temp->data->running_time, temp->data->waiting_time,temp->data->block_time);
			printf("-------------------out RRR--------------------\n");

		}
		//Node* current = create_Node();
		if (strcmp(all_events[j], "I") == 0){
			printf("IIIIIIIIIIIIIIIIhello\n");
			Node* current = create_Node();
			current = block_front;
			printf("block_front pid: %d ", block_front->data->PID );
			printf("IIIIIIIIIIIIIIIIhello\n");
			printf("current pid: %d ", current->data->PID );

			printf("current process_id: %d ", process_id[j] );
			while (current->data->PID != process_id[j]){
				current = current->next;
			}
			printf("IIIIIIIIIIIIIIIIhello\n");
			current->data->current_time = all_time[j];////////////////
			printf("while\n");
			if (current == block_front && current == block_rear){///if there's only one in block
				printf("IIIIIIIIIIIIIIIIhello1111\n");
				//current->next->pre = NULL;
				block_front = NULL;
				block_rear = NULL;
				printf("IIIIIIIIIIIIIIIIhello1111\n");
			}
			if(current == block_front && current != block_rear){///if delete first
				printf("IIIIIIIIIIIIIIIIhello22222222\n");
				current->next->pre = NULL;
				block_front = current->next;
			}
			if(current != block_front && current == block_rear){/// if delete last
				printf("IIIIIIIIIIIIIIIIhello3333333333\n");
				current->pre->next = NULL;
				block_rear = current->pre;
			}
			if(current->next != NULL && current->pre != NULL){
				current->pre->next = current->next;//if delete middle
				current->next->pre = current->pre;
			}
			current->data->block_time = current->data->block_time + (all_time[j] - current->data->start_block);
			//temp->data->block_time = temp->data->block_time + (all_time[j] - temp->data->start_block);
			if (is_empty_queue(ready_queue) == 0){
				current->data->start_running = all_time[j];
				enQueue(running_queue, current);
			}
			else{
				current->data->start_ready = all_time[j];
				enQueue(ready_queue, current);

			}
			printf("isEmpty in ready in IIIII: %d     ",is_empty_queue(ready_queue));////
			printf("-----------------out IIII----------------------\n");
		}
		if (strcmp(all_events[j], "T") == 0){

			printf("TTTTTTTTTTTTTTThello\n");////
			temp = deQueue(running_queue);
			temp->data->current_time = all_time[j];/////
			temp->data->start_ready = all_time[j];
			temp->data->running_time = temp->data->running_time + (all_time[j] - temp->data->start_running);
			enQueue(ready_queue,temp);

			temp = deQueue(ready_queue);
			temp->data->current_time = all_time[j];/////
			temp->data->start_running = all_time[j];
			temp->data->waiting_time = temp->data->waiting_time + (all_time[j] - temp->data->start_ready);
			enQueue(running_queue, temp);
		}
		if (strcmp(all_events[j], "E") == 0){
			printf("EEEEEEEEEEEEEhello    ");///
			printf("isEmpty: %d     ",is_empty_queue(running_queue));///
			temp = deQueue(running_queue);
			printf("deQueue pid: %d       ", temp->data->PID);///
			printf("all time %d   ", all_time[j]);
			temp->data->running_time = temp->data->running_time + (all_time[j] - temp->data->start_running);
			printf("temp's running time:%d     ", temp->data->running_time);////

			total_running_time = total_running_time + temp->data->running_time;

			enQueue(exist_queue, temp);//you ke neng you wen ti
			printf("isEmpty in exist after enQueu exist: %d     ",is_empty_queue(exist_queue));///
			printf("isEmpty in ready: %d   /n  ",is_empty_queue(ready_queue));////
			if (is_empty_queue(ready_queue) != 0){
				temp = deQueue(ready_queue);
				temp->data->start_running = all_time[j];
				temp->data->waiting_time = temp->data->waiting_time + (all_time[j] - temp->data->start_ready);
				enQueue(running_queue, temp);
				printf("isEmpty last: %d     \n",is_empty_queue(running_queue));
			}

		}
	}
	///////////////////////////////////////////
	//temp = deQueue(running_queue);
	//printf("running: %d", temp->data->PID);
	//////////////////////////////////////////
	printf("total time: %d   \n", all_time[a-1]);
	printf("0 %d\n", all_time[a-1] - total_running_time);
	for (int m=1;m<=number_process;m++){
		temp = deQueue(exist_queue);
		while (is_empty_queue(exist_queue) == 1 && temp->data->PID != m){
			enQueue(exist_queue, temp);
			temp = deQueue(exist_queue);
		}
		printf("%d %d %d %d\n", temp->data->PID, temp->data->running_time, temp->data->waiting_time, temp->data->block_time);
	}
	/*
	while(is_empty_queue(exist_queue) == 1){
		printf("%d %d %d %d\n", exist_queue->front->data->PID, exist_queue->front->data->running_time, exist_queue->front->data->waiting_time, exist_queue->front->data->block_time);
		deQueue(exist_queue);
		//exist_queue->front = exist_queue->front->next;

	}
	*/
	return (0);
}

