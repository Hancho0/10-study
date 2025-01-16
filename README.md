#개인정보 출력 도킹펜

[리소스 뷰] 에서 DIALOG를 삽입 한 후 Style 을 Child, Border를 None으로 설정한다.

그리고 나서 Group Box, Edit Box, Combo Box, Radio Button, List Box, Check Box, Button을 배치하고 각각 속성을 건들여준다.

그리고 난 후 [ MFC 클래스 추가 ] CInfoPnae 를 해준다.

그리고 난 후 [멤버 변수] 탭에서 Edit Box, Combo Box, List Box 각 연결할 제어 변수를 설정한다.

| 컨트롤 ID | 범주 | 이름 | 변수 형식 |
|---|---|---|---|
| IDC_EDIT_NAME | 값 | m_strName | CString |
| IDC_COMBO_SEX | 컨트롤 | m_CbSex | CComboBox |
| IDC_LIST_HOBBY | 컨트롤 | m_listHobby | CListBox |

그리고 나서 클래스에서 [ 가상 함수 ] 에서 OnInitDialog 함수를 추가한 후 초기화 작업을 해준다.

```ruby
BOOL CInfoCtrl::OnInitDialog()
{
	CDialogEx::OnInitDialog();

	// TODO:  여기에 추가 초기화 작업을 추가합니다.
	m_listHobby.AddString(_T("독 서"));
	m_listHobby.AddString(_T("낚 시"));
	m_listHobby.AddString(_T("운 동"));

	m_cbSex.SetCurSel(0);

	((CButton*)GetDlgItem(IDC_RADIO_MALE))->SetCheck(TRUE);

	return TRUE;  // return TRUE unless you set the focus to a control
				  // 예외: OCX 속성 페이지는 FALSE를 반환해야 합니다.
}

```

그리고 [ MFC 클래스 추기 ] CInfoCtrl 을 추가한다.\n
새로 생성한 클래스에다가 변수를 추가해준다.

그리고 기존 CInfoPane 클래스의 헤더파일에 CInfoCtrl 클래스의 헤더파일을 include 시킨다.

```ruby
#include "CinfoCtrl.h"
```

그리고 난 후 CInfoPane 클래스 마법사에서 [ 메시지 ] 에서 WM_CREATE를 선택 해서 처리기를 추가한 후 다음 코드를 작성한다.

```ruby
int CInfoPane::OnCreate(LPCREATESTRUCT lpCreateStruct)
{
	if (CDockablePane::OnCreate(lpCreateStruct) == -1)
		return -1;

	// TODO:  여기에 특수화된 작성 코드를 추가합니다.
	if (!m_ctrlinfo.Create(IDD_DIALOG_INFO, this))
	{
		TRACE0("개인 정보 윈도우를 만들지 못했습니다.");
		return -1;
	}
	m_ctrlinfo.ShowWindow(SW_SHOW);

	return 0;
}
```

그리고 난 후 CInfoPane 클래스 마법사에서 [ 메시지] 에서 WM_SIZE를 선택 해서 처리기를 추가한후 다음 코드를 작성한다.

```ruby
void CInfoPane::OnSize(UINT nType, int cx, int cy)
{
	CDockablePane::OnSize(nType, cx, cy);

	// TODO: 여기에 메시지 처리기 코드를 추가합니다.
	if (m_ctrlinfo.GetSafeHwnd())
	{
		m_ctrlinfo.MoveWindow(0, 0, cx, cy);
		m_ctrlinfo.SetFocus();
	}
}
```

그리고 난 후 [ 클래스 뷰 ] 에서 변수를 추가한다 [ CInfoPane / m_paneinfo ]

그리고 CMainFrame 클래스의 헤더파일에 CInfoPane 클래스의 헤더파일을 include 시킨다.

```ruby
#include "CInfoPane.h"
```

그리고 난 후 CMainFrame 클래스의 OnCreate 함수에다가 코드를 추가한다.

```ruby
	if (!m_paneInfo.Create(_T("개인 정보"), this, CRect(0, 0, 200, 200), TRUE, 0xfafa, WS_CHILD | WS_VISIBLE | WS_CLIPSIBLINGS | WS_CLIPCHILDREN | CBRS_RIGHT | CBRS_FLOAT_MULTI))
	{
		TRACE0("도킹 팬(Pane)을 만들지 못했습니다.\n");
		return -1; //만들지 못했습니다.
	}
	m_paneInfo.SetMinSize(300);

	//도킹할 수 있도록 함
	m_paneInfo.EnableDocking(CBRS_ALIGN_ANY);
	DockPane(&m_paneInfo);

	return 0;
```

그리고 난 후 CInfoCtrl 클래스에 필요한 멤버 변수를 추가한다. [ bool m_bSex ] [ bool m_bChecked[3] ]

그리고 CInfoCtrl 클래스의 생성자 함수인 CInfoCtrl 함수로 이동하여 변수를 추기화 해준다.

```ruby
	m_bSex = true;
	m_bChecked[0] = m_bChecked[1] = m_bChecked[2] = false;
```

그리고 난 후 [ 클래스 마법사 ] 에서 CInfoCtrl 클래스의 명령에서 IDC_COMBO_SEX를 선택하여 명령 핸들러 함수를 추가한후 다음 코드를 추가한다. 

```ruby
void CInfoCtrl::OnSelchangeComboSex()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	int index = m_cbSex.GetCurSel();
	switch (index)
	{
	case 0:
		m_bSex = true;
		((CButton*)GetDlgItem(IDC_RADIO_MALE))->SetCheck(TRUE);
		((CButton*)GetDlgItem(IDC_RADIO_FEMALE))->SetCheck(FALSE);
		break;
	case 1:
		m_bSex = false;
		((CButton*)GetDlgItem(IDC_RADIO_MALE))->SetCheck(FALSE);
		((CButton*)GetDlgItem(IDC_RADIO_FEMALE))->SetCheck(TRUE);
	}
}
```

그리고 위와 똑같은 방법으로 IDC_RADIO_MALE를 선택하여 명령어 핸들러 함수를 만든 후 다음 코드를 추가한다.

```ruby
void CInfoCtrl::OnRadioMale()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	m_bSex = true;
	m_cbSex.SetCurSel(0);
}
```

똑같이 IDC_RADIO_FEMALE를 선택하여 코드를 추가한다.

```ruby
void CInfoCtrl::OnRadioFemale()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	m_bSex = false;
	m_cbSex.SetCurSel(1);
}
```

그리고 똑같은 방법으로 IDC_LIST_HOBBY 명령어 핸들러 함수를 추가하여 다음 코드를 추가한다.

```ruby
void CInfoCtrl::OnSelchangeListHobby()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	int index = m_listHobby.GetCurSel();
	switch (index)
	{
	case 0:
		if (!m_bChecked[0])
			((CButton*)GetDlgItem(IDC_CHECK_READING))->SetCheck(TRUE);
		else
			((CButton*)GetDlgItem(IDC_CHECK_READING))->SetCheck(FALSE);
		m_bChecked[0] = !m_bChecked[0];
		break;
	case 1:
		if (!m_bChecked[1])
			((CButton*)GetDlgItem(IDC_CHECK_FISHING))->SetCheck(TRUE);
		else
			((CButton*)GetDlgItem(IDC_CHECK_FISHING))->SetCheck(FALSE);
		m_bChecked[1] = !m_bChecked[1];
		break;
	case 2:
		if (!m_bChecked[2])
			((CButton*)GetDlgItem(IDC_CHECK_SPORTS))->SetCheck(TRUE);
		else
			((CButton*)GetDlgItem(IDC_CHECK_SPORTS))->SetCheck(FALSE);
		m_bChecked[2] = !m_bChecked[2];
		break;
	}
}
```

그리고 똑같이 IDC_CHECK_READING 함수를 추가하여 해당되는 Check Box를 선택하면 그에 해당하는 List Box의 아이템이 선택되게 코드를 작성한다.

```ruby
void CInfoCtrl::OnClickedCheckReading()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	if (m_bChecked[0] == false)
		m_listHobby.SetSel(0, 1);
	else
		m_listHobby.SetSel(0, 0);
	m_bChecked[0] = !m_bChecked[0];
}
```

똑같은 방법으로 Fishing 하고 Sports 도 똑같이 해준다.

```ruby
void CInfoCtrl::OnClickedCheckFishing()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	if (m_bChecked[1] == false)
		m_listHobby.SetSel(1, 1);
	else
		m_listHobby.SetSel(1, 0);
	m_bChecked[1] = !m_bChecked[1];
}


void CInfoCtrl::OnClickedCheckSports()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	if (m_bChecked[2] == false)
		m_listHobby.SetSel(2, 1);
	else
		m_listHobby.SetSel(2, 0);
	m_bChecked[2] = !m_bChecked[2];
}
```

그리고 난 후 View 클래스에다가 변수를 추가해준다.

| 자료형 | 변수명 | 의미 |
|---|---|---|
| CString | m_strName | 이름을 저장하는 변수 |
| CString | m_strSex | 성별을 저장하는 변수 |
| CString | m_strHobby | 취미를 저장하는 변수 |

그리고 난 후 [ 클래스 마법사 ] 에서 CInfoCtrl 클래스의 명령에서 IDC_BUTTON_RESULT를 선택하여 명령 핸들러 함수를 추가한후 다음 코드를 추가한다. 

```ruby
void CInfoCtrl::OnClickedButtonResult()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	CMainFrame *pFrame = (CMainFrame *)AfxGetMainWnd();
	CProject1View *pView = (CProject1View*)pFrame->GetActiveView();
	UpdateData(TRUE);
	pView->m_strName = _T("이름 : ");
	pView->m_strName += m_strName;

	pView->m_strSex = _T("성별 : ");
	if (m_bSex)
	{
		pView->m_strSex += _T("남자");
	}
	else
	{
		pView->m_strSex += _T("여자");
	}

	pView->m_strHobby = _T("내가 선택한 취미 : ");
	if (m_bChecked[0])
	{
		pView->m_strHobby += _T("독서");
	}
	if (m_bChecked[1])
	{
		pView->m_strHobby += _T("낚시");
	}
	if (m_bChecked[2])
	{
		pView->m_strHobby += _T("운동");
	}
	pView->Invalidate();
}
```

그리고 CInfoCtrl.cpp 구현 파일에 "MainFrm.h", "Project1Doc.h", "Project1View.h" 를 include 해준다.

```ruby
#include "MainFrm.h"
#include "Project1Doc.h"
#include "Project1View.h"
```

그리고 난 후 Project1View 클래스에서 OnDraw 함수 에서 주석 처리 해제 후 그리기 코드를 추가한다. 

```ruby
	pDC->TextOut(20, 20, m_strName);
	pDC->TextOut(20, 50, m_strSex);
	pDC->TextOut(20, 80, m_strHobby);
```

[ 출력 결과 ]

![image](https://github.com/user-attachments/assets/d10dadba-df8a-4d8c-9100-5f08839ff61f)

![image](https://github.com/user-attachments/assets/f84774ee-c517-4f8e-8619-5cc1265469d3)
