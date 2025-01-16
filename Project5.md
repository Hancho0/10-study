![image](https://github.com/user-attachments/assets/d08d8234-7f68-49d0-9872-f0ba83df39a5)
![image](https://github.com/user-attachments/assets/b142cdca-2c7a-4649-be9d-c26a80c95b83)
![image](https://github.com/user-attachments/assets/75ca1685-1a67-47e8-b03f-8cd2df886dc6)

```ruby
void CProject5cDlg::OnPaint()
{
	if (IsIconic())
	{
		CPaintDC dc(this); // 그리기를 위한 디바이스 컨텍스트입니다.

		SendMessage(WM_ICONERASEBKGND, reinterpret_cast<WPARAM>(dc.GetSafeHdc()), 0);

		// 클라이언트 사각형에서 아이콘을 가운데에 맞춥니다.
		int cxIcon = GetSystemMetrics(SM_CXICON);
		int cyIcon = GetSystemMetrics(SM_CYICON);
		CRect rect;
		GetClientRect(&rect);
		int x = (rect.Width() - cxIcon + 1) / 2;
		int y = (rect.Height() - cyIcon + 1) / 2;

		// 아이콘을 그립니다.
		dc.DrawIcon(x, y, m_hIcon);
	}
	else
	{
		CDialogEx::OnPaint();
	}

	CRect rectView, rectFigure;
	GetDlgItem(IDC_STATIC_VIEW)->GetWindowRect(&rectView);
	CPoint ptCenter = rectView.CenterPoint();
	ScreenToClient(&ptCenter);

	rectFigure.left = ptCenter.x - (int)(rectView.Width() / 2.0f * m_dlgRatio.m_nCurHScale / 100.0f);
	rectFigure.right = ptCenter.x + (int)(rectView.Width() / 2.0f * m_dlgRatio.m_nCurHScale / 100.0f);
	rectFigure.top = ptCenter.y - (int)(rectView.Height() / 2.0f * m_dlgRatio.m_nCurVScale / 100.0f);
	rectFigure.bottom = ptCenter.y + (int)(rectView.Height() / 2.0f * m_dlgRatio.m_nCurVScale / 100.0f);

	CClientDC dc(this);
	CBrush NewBrush, *oldBrush;

	NewBrush.CreateSolidBrush(m_dlgColor.m_colorObject);
	oldBrush = dc.SelectObject(&NewBrush);

	switch (m_dlgObject.m_nSelObject)
	{
	case 1:
		dc.Rectangle(&rectFigure);
		break;
	case 2:
		dc.Ellipse(&rectFigure);
		break;
	}

	dc.SelectObject(oldBrush);
	NewBrush.DeleteObject();
}

// 사용자가 최소화된 창을 끄는 동안에 커서가 표시되도록 시스템에서
//  이 함수를 호출합니다.
HCURSOR CProject5cDlg::OnQueryDragIcon()
{
	return static_cast<HCURSOR>(m_hIcon);
}



void CProject5cDlg::UpdateDrawing()
{
	// TODO: 여기에 구현 코드 추가.
	CRect rectView;
	GetDlgItem(IDC_STATIC_VIEW)->GetWindowRect(&rectView);
	ScreenToClient(&rectView);
	rectView.DeflateRect(1, 1, 1, 1);

	InvalidateRect(&rectView);
}


void CProject5cDlg::OnSelchangeTabSelection(NMHDR *pNMHDR, LRESULT *pResult)
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	int nSelection = m_tabSelection.GetCurSel();

	switch (nSelection)
	{
	case 0:
		m_dlgObject.ShowWindow(SW_SHOW);
		m_dlgColor.ShowWindow(SW_HIDE);
		m_dlgRatio.ShowWindow(SW_HIDE);
		break;
	case 1:
		m_dlgObject.ShowWindow(SW_HIDE);
		m_dlgColor.ShowWindow(SW_SHOW);
		m_dlgRatio.ShowWindow(SW_HIDE);
		break;
	case 2:
		m_dlgObject.ShowWindow(SW_HIDE);
		m_dlgColor.ShowWindow(SW_HIDE);
		m_dlgRatio.ShowWindow(SW_SHOW);
		break;
	}
	*pResult = 0;
}
```

```ruby
BOOL CObjectDlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();

	// TODO:  여기에 추가 초기화 작업을 추가합니다.
	m_nSelObject = 1;
	((CButton*)GetDlgItem(IDC_RADIO_RECT))->SetCheck(TRUE);

	return TRUE;  // return TRUE unless you set the focus to a control
				  // 예외: OCX 속성 페이지는 FALSE를 반환해야 합니다.
}


void CObjectDlg::OnRadioRect()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CProject5cDlg* pMainDlg = (CProject5cDlg*)AfxGetMainWnd();

	m_nSelObject = 1;
	pMainDlg->UpdateDrawing();
}


void CObjectDlg::OnRadioCircle()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CProject5cDlg* pMainDlg = (CProject5cDlg*)AfxGetMainWnd();

	m_nSelObject = 2;
	pMainDlg->UpdateDrawing();
}
```

```ruby
BOOL CColorDlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();

	// TODO:  여기에 추가 초기화 작업을 추가합니다.
	m_colorObject = RGB(255, 0, 0);
	m_sliderRed.SetRange(0, 255);
	m_sliderGreen.SetRange(0, 255);
	m_sliderBlue.SetRange(0, 255);
	m_sliderRed.SetPos(255);
	m_sliderGreen.SetPos(0);
	m_sliderBlue.SetPos(0);
	m_nRed = 255;
	m_nGreen = 0;
	m_nBlue = 0;
	UpdateData(FALSE);

	return TRUE;  // return TRUE unless you set the focus to a control
				  // 예외: OCX 속성 페이지는 FALSE를 반환해야 합니다.
}


void CColorDlg::OnHScroll(UINT nSBCode, UINT nPos, CScrollBar* pScrollBar)
{
	// TODO: 여기에 메시지 처리기 코드를 추가 및/또는 기본값을 호출합니다.
	CProject5cDlg* pMainDlg = (CProject5cDlg*)AfxGetMainWnd();

	if (pScrollBar->GetSafeHwnd() == m_sliderRed.m_hWnd)
	{
		m_nRed = m_sliderRed.GetPos();
	}
	else if (pScrollBar->GetSafeHwnd() == m_sliderGreen.m_hWnd)
	{
		m_nGreen = m_sliderGreen.GetPos();
	}
	else if (pScrollBar->GetSafeHwnd() == m_sliderBlue.m_hWnd)
	{
		m_nBlue = m_sliderBlue.GetPos();
	}
	else
		return;

	m_colorObject = RGB(m_nRed, m_nGreen, m_nBlue);
	UpdateData(FALSE);
	pMainDlg->UpdateDrawing();;
	CDialogEx::OnHScroll(nSBCode, nPos, pScrollBar);
}
```

```ruby
BOOL CRatioDlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();

	// TODO:  여기에 추가 초기화 작업을 추가합니다.
	m_bSameRatio = TRUE;
	((CButton*)GetDlgItem(IDC_CHECK_SAME_RATIO))->SetCheck(TRUE);

	m_sliderHorizontal.SetRange(0, 100);
	m_sliderVertical.SetRange(0, 100);
	m_sliderHorizontal.SetPos(50);
	m_sliderVertical.SetPos(50);

	m_nHorizontal = 50;
	m_nVertical = 50;
	m_nCurHScale = 50;
	m_nCurVScale = 50;
	UpdateData(FALSE);

	return TRUE;  // return TRUE unless you set the focus to a control
				  // 예외: OCX 속성 페이지는 FALSE를 반환해야 합니다.
}


void CRatioDlg::OnHScroll(UINT nSBCode, UINT nPos, CScrollBar* pScrollBar)
{
	// TODO: 여기에 메시지 처리기 코드를 추가 및/또는 기본값을 호출합니다.
	CProject5cDlg* pMainDlg = (CProject5cDlg*)AfxGetMainWnd();

	m_nCurHScale = m_sliderHorizontal.GetPos();
	m_nCurVScale = m_sliderVertical.GetPos();
	if (pScrollBar->GetSafeHwnd() == m_sliderHorizontal.m_hWnd)
	{
		if (m_bSameRatio == TRUE)
		{
			m_sliderVertical.SetPos(m_nCurHScale);
		}
	}
	else if (pScrollBar->GetSafeHwnd() == m_sliderVertical.m_hWnd)
	{
		if (m_bSameRatio == TRUE)
		{
			m_sliderHorizontal.SetPos(m_nCurVScale);
		}
	}
	else
		return;

	m_nHorizontal = m_nCurHScale;
	m_nVertical = m_nCurVScale;
	UpdateData(FALSE);
	pMainDlg->UpdateDrawing();

	CDialogEx::OnHScroll(nSBCode, nPos, pScrollBar);
}


void CRatioDlg::OnClickedCheckSameRatio()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	UpdateData(TRUE);
	CProject5cDlg* pMainDlg = (CProject5cDlg*)AfxGetMainWnd();

	m_bSameRatio = !m_bSameRatio;
	if (m_bSameRatio == TRUE)
	{
		if (m_nCurHScale > m_nCurVScale)
		{
			m_nHorizontal = m_sliderHorizontal.GetPos();
			m_nVertical = m_nHorizontal;
		}
		else
		{
			m_nVertical = m_sliderVertical.GetPos();
			m_nHorizontal = m_nVertical;
		}
		m_nCurHScale = m_nHorizontal;
		m_nCurVScale = m_nVertical;
		m_sliderHorizontal.SetPos(m_nCurVScale);
		m_sliderVertical.SetPos(m_nCurVScale);
		UpdateData(FALSE);
	}

	pMainDlg->UpdateDrawing();
}
```
