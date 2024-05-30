#include <stdio.h>
#include <stdlib.h>
#include <Windows.h>
#include <time.h>

#define LEN_MIN 15
#define LEN_MAX 50
#define PROB_MIN 10
#define PROB_MAX 90
#define STM_MIN 0 // 마동석 체력
#define STM_MAX 5
#define PROB_MIN 10 // 확률
#define PROB_MAX 90
#define AGGRO_MIN 0 // 어그로 범위
#define AGGRO_MAX 5
// 마동석 이동 방향
#define MOVE_LEFT 1
#define MOVE_STAY 0
// 좀비의 공격 대상
#define ATK_NONE 0
#define ATK_CITIZEN 1
#define ATK_DONGSEOK 2
// 마동석 행동
#define ACTION_REST 0
#define ACTION_PROVOKE 1
#define ACTION_PULL 2

int len; // 입력한 열차 길이
int per; // 입력한 확률
int stm; // 마동석 체력
int loc_c, loc_z, loc_m; // 시민, 좀비, 마동석 위치
int c_pre, z_pre; // 시민, 좀비 이전 위치
int v_turn_z = 1; // 좀비 이동 주기
int aggro = 1; // 마동석 어그로
char train[LEN_MAX + 1]; // 열차 상태 배열

// 열차 상태 업데이트를 담당하는 함수
void update_train() {
    c_pre = loc_c;
    z_pre = loc_z;

    if (rand() % 100 >= (per - 1)) loc_c--;
    if ((v_turn_z % 2 == 1) && (rand() % 100 < (per - 1))) loc_z--;
}

// 열차 상태를 출력하는 함수
void display_train() {
    for (int x = 0; x < len; x++) printf("#");
    printf("\n");

    for (int x = 0; x < len; x++) {
        if (x == 0 || x == (len - 1)) printf("#");
        else if (x == loc_z) printf("Z");
        else if (x == loc_c) printf("C");
        else if (x == loc_m) printf("M");
        else printf(" ");
    }
    printf("\n");

    for (int x = 0; x < len; x++) printf("#");
    printf("\n");

   
}

void display_cz() {
    if (c_pre == loc_c) printf("citizen: stay %d\n", loc_c);
    else printf("citizen: %d -> %d\n", c_pre, loc_c);

    if (v_turn_z % 2 == 0) printf("zombie: stay %d (cannot move)\n", loc_z);
    else if (z_pre == loc_z) printf("zombie: stay %d\n", loc_z);
    else printf("zombie: %d -> %d\n", z_pre, loc_z);
}
void display_mds() {
    printf("madongseok: stay %d (aggro: %d, stamina: %d)\n", loc_m, aggro, stm); // 마동석 위치와 상태 출력
}

// 게임 종료 조건을 체크하는 함수
void check_status() {
    if (loc_c == 1) {
        printf("\nSUCCESS!\nCitizen(s) escaped to the next train\n");
        exit(0);
    }
    if (loc_z - loc_c == 1) {
        printf("\nGAME OVER!\nCitizen(s) has(have) been attacked by a zombie\n");
        exit(0);
    }
    v_turn_z++;
}

// 각 캐릭터의 위치를 업데이트하는 함수
void update_characters() {
    // 시민 위치 업데이트
    if (rand() % 100 >= (per - 1)) loc_c--;

    // 좀비 위치 업데이트
    if ((v_turn_z % 2 == 1) && (rand() % 100 < (per - 1))) loc_z--;

    // 마동석 위치 업데이트
    // 여기에 마동석의 움직임 업데이트 로직 추가
}

// 초기화 및 메인 루프를 담당하는 역할 함수
void role() {
    printf("부산헹!!!\n");

    do {
        printf("train length(15~50)>> ");
        scanf_s("%d", &len);
    } while (len < LEN_MIN || len > LEN_MAX);

    do {
        printf("madongseok stamina(0~5)>> ");
        scanf_s("%d", &stm);
    } while (stm < STM_MIN || stm > STM_MAX);

    do {
        printf("percentile probability 'p'(10~90)>> ");
        scanf_s("%d", &per);
    } while (per < PROB_MIN || per > PROB_MAX);

    loc_c = len - 6;
    loc_z = len - 3;
    loc_m = len - 2;

    srand((unsigned int)time(NULL)); // rand() 시드 초기화

    while (1) {
        update_characters();
        display_train();
        display_cz();
        check_status();

        int move;
        do {
            printf("madongseok move(0:stay, 1:left)>> ");
            scanf_s("%d", &move);
        } while (move != 0 && move != 1);

        if (move == 1 && loc_m > 1) {
            loc_m--; // 마동석을 왼쪽으로 이동
            aggro++; // 마동석이 움직였으므로 어그로 증가
        }
        else {
            aggro--; // 마동석이 움직이지 않았으므로 어그로 감소
        }

        update_train(); // 열차 상태 업데이트
        display_train(); // 열차 상태 출력
        display_mds();
        printf("\n"); // 줄바꿈
        check_status(); // 게임 종료 조건 체크
    }
}

int main(void) {
    role();
    return 0;
}
