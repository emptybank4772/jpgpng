#include <SDL.h>
#include <SDL_image.h>
#include <stdio.h>

int main(int argc, char* argv[]) {
    // SDL 초기화
    if (SDL_Init(SDL_INIT_VIDEO) < 0) {
        printf("SDL 초기화 실패: %s\n", SDL_GetError());
        return -1;
    }

    // 이미지 라이브러리 초기화 (JPG, PNG 형식 지원)
    if (!(IMG_Init(IMG_INIT_JPG | IMG_INIT_PNG) & (IMG_INIT_JPG | IMG_INIT_PNG))) {
        printf("이미지 초기화 실패: %s\n", IMG_GetError());
        SDL_Quit();
        return -1;
    }

    // 윈도우 생성
    SDL_Window* window = SDL_CreateWindow("이미지 출력", SDL_WINDOWPOS_UNDEFINED, SDL_WINDOWPOS_UNDEFINED, 800, 600, SDL_WINDOW_SHOWN);
    if (!window) {
        printf("윈도우 생성 실패: %s\n", SDL_GetError());
        IMG_Quit();
        SDL_Quit();
        return -1;
    }

    // 렌더러 생성
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_ACCELERATED);
    if (!renderer) {
        printf("렌더러 생성 실패: %s\n", SDL_GetError());
        SDL_DestroyWindow(window);
        IMG_Quit();
        SDL_Quit();
        return -1;
    }

    // 이미지 로드 (파일 경로는 실제 이미지 파일이 있는 경로로 설정)
    SDL_Texture* image1 = IMG_LoadTexture(renderer, "jcshim.jpg");
    SDL_Texture* image2 = IMG_LoadTexture(renderer, "jcshim.png");

    if (!image1 || !image2) {
        printf("이미지 로드 실패: %s\n", IMG_GetError());
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        IMG_Quit();
        SDL_Quit();
        return -1;
    }

    // 이벤트 루프
    SDL_Event e;
    int quit = 0;
    while (!quit) {
        // 이벤트 처리
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_QUIT) {
                quit = 1;
            }
        }

        // 화면 지우기
        SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);  // 배경을 검정색으로 설정
        SDL_RenderClear(renderer);

        // 두 번째 이미지를 좌측 상단에 출력
        SDL_Rect dstRect1 = { 50, 100, 300, 200 };  // 위치 (50, 100), 크기 (300x200)
        SDL_RenderCopy(renderer, image1, NULL, &dstRect1);

        // 첫 번째 이미지를 우측 상단에 출력
        SDL_Rect dstRect2 = { 400, 100, 300, 200 };  // 위치 (400, 100), 크기 (300x200)
        SDL_RenderCopy(renderer, image2, NULL, &dstRect2);

        // 화면 업데이트
        SDL_RenderPresent(renderer);

        // 잠시 대기
        SDL_Delay(3000);  // 3초 동안 화면에 이미지가 표시됨
    }

    // 리소스 해제
    SDL_DestroyTexture(image1);
    SDL_DestroyTexture(image2);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);

    // 종료 처리
    IMG_Quit();
    SDL_Quit();

    return 0;
}
