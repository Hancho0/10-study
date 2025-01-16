# 그림판 만들기

[ 애플리케이션 종류 ] "단일 문서" 선택하여 SDI 기반의 프로젝트를 만들고 [ 프로젝트 스타일 ] "MFC Standard"을, [ 비주얼 스타일 및 색 ] "Windows Native/Default"를 선택한다.\n
[ 사용자 인터페이스 기능 ] 단계에서 [명령 모음(메뉴/도구 모음/리본)] 항목에서 "메뉴 모음 및 도구  모음 사용"을 선택한다.

GDI+ 라이브러리를 초기화한다.\n
pch.h 헤더 파일에 헤더파일과 라리으버리를 include 시키고 네임스페이스를 선언하는 코드를 작성한다.

```ruby
#include <gdiplus.h>
#pragma comment(lib, "gdiplus")
using namespace Gdiplus;
```

App 클래스의 InitInstance(0 함수로 이동하여 GDI+ 라이브러리를 초기화 하는 코드를 추가한다.

```ruby
ULONG_PTR gdiplusToken;

// CProject2App 초기화

BOOL CProject2App::InitInstance()
{
	GdiplusStartupInput gdiplusStartupInput;
	if (::GdiplusStartup(&gdiplusToken, &gdiplusStartupInput, NULL) != Ok)
	{
		AfxMessageBox(_T("GDI+ 라이브러리의 초기화에 실패했습니다!"));
		return false;
	}
```

App 클래스에서 ExitInstance() 함수로 이동하여 GDI+ 라이브러리를 해제하는 코드를 추가한다.
```ruby
int CProject2App::ExitInstance()
{
	//TODO: 추가한 추가 리소스를 처리합니다.
	AfxOleTerm(FALSE);
	::GdiplusShutdown(gdiplusToken);

	return CWinAppEx::ExitInstance();
}
```

[ 리소스 뷰 ]의 Menu에서 IDR_MAINFRAME에서 [ 그리기 ] 와 [ 도움말 ] 메뉴만 가진다.

![image](https://github.com/user-attachments/assets/e2d8286f-55ef-45c1-bdb9-5c059bd97951)

각각 메뉴의 속성을 설정하고

툴바 비트맵을 편집한다.\n
[ 리소스 뷰] 의 Tollbar에서 IDR_MAINFRAME_256을 클리가형 툴바 비트맵 편집기가 나타나면 첫 번째 툴바 이외에 모든 툴바를 삭제하고 다음과 같이 툴바 3개를 추가한다.

![image](https://github.com/user-attachments/assets/c4b4bfca-0b76-4697-86c8-733c714b2059)

툭바에 각각 ID_FILE_NEW, ID_PEN_SIZE, ID_PEN_COLOR, ID_ERASER_SIZE ID를 설정한다.

그리고 난 후 커서 리로스를 추가한다.

![image](https://github.com/user-attachments/assets/8db19c18-f88b-4b36-bf44-fa99786746c0)

펜을 나타내는 커서를 생성 한 후 속성에서 ID를 IDC_CURSOR_PEN 으로 설정한다

![image](https://github.com/user-attachments/assets/d1c8b1d2-f4a1-4a89-9da6-3a976ef42abf)

그리고 난 후 똑같이 지우개를 나타내는 커서를 생성 한 후 속성에서 ID를 IDC_CURSOR_ERASER로 설정한다.

그리고 Dialog 를 삽입하여 Static Text, Edit Control, Spin Control을 순서대로 배치하고 설정들을 해준다.

그리고 나서 [ MFC 클래스 추가 ] 를 하여 CPenSizeDlg 를 생성한다.

그리고 또 똑같은 Dialog를 하나더 만들어준후 [ MFC 클래스 추가 ] 를 CERaserSizeDlg 를 생성한다.

[ 클래스 마법사 ] 에서 CPenSizeDlg 클래스 에서 [ 멤버 변수 ] 에서 다음과 같이 설정한다.
| 컨트롤 ID | 범주 | 이름 | 변수 형식 |
|---|---|---|---|
| IDC_EDIT_PEN | 값 | m_nPenSzie | UINT |
| IDC_SPIN_PEN | 컨트롤 | m_spinPen | CSpinButtonCtrl |

그 다음 CEraserSizeDlg 에서도 똑같이 해준다.
| 컨트롤 ID | 범주 | 이름 | 변수 형식 |
|---|---|---|---|
| IDC_EDIT_ERASER | 값 | m_nEraserSize | UINT |
| IDC_SPIN_ERASER | 컨트롤 | m_spinEraser | CSpinButtonCtrl |

그리고 난 후 [ 클래스 마법사 ] 에서 CPenSizeDlg 클래스를 선택 후 [ 가상 함수 ] 에서 OnInitDialog 함수를 추가하여 다음 코드를 작성한다.

```ruby
BOOL CPenSizeDlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();

	// TODO:  여기에 추가 초기화 작업을 추가합니다.
	CMainFrame *pFrame = (CMainFrame*)AfxGetMainWnd();
	CProject2View *pView = (CProject2View*)pFrame->GetActiveView();

	m_spinPen.SetRange(1, 10);
	m_nPenSize = pView->m_nPenSize;
	UpdateData(FALSE);

	return TRUE;  // return TRUE unless you set the focus to a control
				  // 예외: OCX 속성 페이지는 FALSE를 반환해야 합니다.
}
```

그리고 나서 뷰 클래스의 포인터를 얻어오기 위해 헤더파일을 소스파일 상단에 include 시킨다.

```ruby
#include "MainFrm.h"
#include "Project2Doc.h"
#include "Project2View.h"
```

그리고 똑같이 CEraserSizeDlg 클래스에서 OnInitDialog() 함수를 추가하여 다음 코드를 작성한다.

```ruby
BOOL CEaserSizeDlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();

	// TODO:  여기에 추가 초기화 작업을 추가합니다.
	CMainFrame *pFrame = (CMainFrame*)AfxGetMainWnd();
	CProject2View *pView = (CProject2View*)pFrame->GetActiveView();

	m_spinEraser.SetRange(2, 20);
	m_nEraserSize = pView->m_nEraserSize;
	UpdateData(FALSE);

	return TRUE;  // return TRUE unless you set the focus to a control
				  // 예외: OCX 속성 페이지는 FALSE를 반환해야 합니다.
}
```

그리고 똑같이 헤더파일을 소스파일 상단에 include 시킨다.

그리고 난 후 View 클래스에 변수를 추가한다.
| 자료형 | 변수명 | 의미 |
|---|---|---|
| int | m_nPenSize | 펜 크기를 저장하는 변수 |
| int | m_nEraserSize | 지우개 크기를 저장하는 변수 |
| COLORREF | m_colorPen | 펜 색상을 저장하는 변수 |
| CPoint | m_ptPrev | 이전 점을 저장하는 변수 |

View 클래스의 생성자 함수로 가서 초기화 코드를 작성한다.
```ruby
CProject2View::CProject2View() noexcept
{
	// TODO: 여기에 생성 코드를 추가합니다.
	m_nPenSize = 2;
	m_nEraserSize = 4;
	m_colorPen = RGB(0, 0, 0);
}
```

[ 클래스 마법사 ] 에서 CProject2View 클래스 선택 후 [ 명령 ] 탭에서 ID_PEN_SIZE 를 추가해 코드를 작성한다.
```ruby
void CProject2View::OnPenSize()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CPenSizeDlg *pdlgPen = new CPenSizeDlg;
	if (pdlgPen->DoModal() == IDOK)
	{
		m_nPenSize = pdlgPen->m_nPenSize;
	}
}
```

그리고 난 후 클래스 소스 파일 상단에 include 한다.
```ruby
#include "CPenSizeDlg.h"
```

그리고 난 후 똑같이 ID_ERASER_SIZE를 선택하여 소스 코드 작성하고 include를 한다.
```ruby
void CProject2View::OnEraserSize()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CEaserSizeDlg *pdlgEraser = new CEaserSizeDlg;
	if (pdlgEraser->DoModal() == IDOK)
	{
		m_nEraserSize = pdlgEraser->m_nEraserSize;
	}
}
```

```ruby
#include "CEraserSizeDlg.h"
```

그리고 난 후 ID_PEN_COLOR도 선택하여 소스 코드를 작성한다.
```ruby
void CProject2View::OnPenColor()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CColorDialog colorDlg;
	if (colorDlg.DoModal() == IDOK)
	{
		m_colorPen = colorDlg.GetColor();
	}
}
```

마우스를 눌렀을 때 메시지 핸들러 함수를 만든다

[ 클래스 마법사 ] 에서 CProject2View 클래스를 선택 후 [ 메시지 ] 항목에서 WM_LBUTTONDOWN 함수를 추가후 코드 작성해준다.

```ruby
void CProject2View::OnLButtonDown(UINT nFlags, CPoint point)
{
	// TODO: 여기에 메시지 처리기 코드를 추가 및/또는 기본값을 호출합니다.
	m_ptPrev = point;
	HCURSOR hCursor = AfxGetApp()->LoadCursor(IDC_CURSOR_PEN);
	SetCursor(hCursor);

	CView::OnLButtonDown(nFlags, point);
}
```

그리고 난 후 똑같이 WM_RBUTTONDOWN 함수를 추가 후 코드를 작성한다.

```ruby
void CProject2View::OnRButtonDown(UINT nFlags, CPoint point)
{
	// TODO: 여기에 메시지 처리기 코드를 추가 및/또는 기본값을 호출합니다.
	m_ptPrev = point;
	HCURSOR hCursor = AfxGetApp()->LoadCursor(IDC_CURSOR_PEN);
	SetCursor(hCursor);

	CView::OnRButtonDown(nFlags, point);
}
```

그리고 똑같이 WM_MOUSEMOVE 함수를 추가한 후 코드를 작성한다.

```ruby
void CProject2View::OnMouseMove(UINT nFlags, CPoint point)
{
	// TODO: 여기에 메시지 처리기 코드를 추가 및/또는 기본값을 호출합니다.
	CClientDC dc(this);
	Graphics graphics(dc);

	Gdiplus::Color clr;
	clr.SetFromCOLORREF(m_colorPen);

	if (nFlags == MK_LBUTTON)
	{
		HCURSOR hCursor = AfxGetApp()->LoadCursor(IDC_CURSOR_PEN);
		SetCursor(hCursor);
		Pen pen(Color(clr), m_nPenSize);
		graphics.DrawLine(&pen, m_ptPrev.x, m_ptPrev.y, point.x, point.y);
		m_ptPrev = point;
	}
	if (nFlags == MK_RBUTTON)
	{
		HCURSOR hCursor = AfxGetApp()->LoadCursor(IDC_CURSOR_ERASER);
		SetCursor(hCursor);
		Pen pen(Color(255, 255, 255), m_nEraserSize);
		graphics.DrawLine(&pen, m_ptPrev.x, m_ptPrev.y, point.x, point.y);
		m_ptPrev = point;
	}

	CView::OnMouseMove(nFlags, point);
}
```

그리고 나서 CProject2View 클래스에서 OnContext 함수에서 주석을 사용하여 문맥 메뉴가 작동하지 못하도록 코드를 수정한다.

```ruby
void CProject2View::OnContextMenu(CWnd* /* pWnd */, CPoint point)
{
#ifndef SHARED_HANDLERS
	//theApp.GetContextMenuManager()->ShowPopupMenu(IDR_POPUP_EDIT, point.x, point.y, this, TRUE);
#endif
}
```

[ 출력 결과 ]
![image](https://github.com/user-attachments/assets/eb38eca8-7622-4682-870a-f2cc36ce779c)
![image](https://github.com/user-attachments/assets/a62193af-8320-4a53-91e6-54c7bb8ab573)
![image](https://github.com/user-attachments/assets/56f9b6b7-05c1-4020-98d9-1d1a5077756f)
![image](https://github.com/user-attachments/assets/f06e6aa7-32ca-4423-9fb3-a3ea9adb225d)
