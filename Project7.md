![image](https://github.com/user-attachments/assets/fdcb4af6-8ee1-4d8e-b27d-8d4d542d884d)
![image](https://github.com/user-attachments/assets/60feee14-2e5e-4466-b031-6e66e91db53a)

```ruby
void CMainFrame::OnComboObject()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CMFCRibbonComboBox* pObject = DYNAMIC_DOWNCAST(CMFCRibbonComboBox, m_wndRibbonBar.FindByID(ID_COMBO_OBJECT));

	CProject7View* pView = (CProject7View*)GetActiveView();
	pView->m_nObjectType = pObject->GetCurSel();
}


void CMainFrame::OnButtonEraser()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CProject7View* pView = (CProject7View*)GetActiveView();
	pView->m_nCount = 0;
	pView->Invalidate();
}


void CMainFrame::OnButtonLinecolor()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CMFCRibbonColorButton* pColor = DYNAMIC_DOWNCAST(CMFCRibbonColorButton, m_wndRibbonBar.FindByID(ID_BUTTON_LINECOLOR));

	CProject7View* pView = (CProject7View*)GetActiveView();
	pView->m_colorLine = pColor->GetColor();
	pView->Invalidate();
}




void CMainFrame::OnButtonFillcolor()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CMFCRibbonColorButton* pColor = DYNAMIC_DOWNCAST(CMFCRibbonColorButton, m_wndRibbonBar.FindByID(ID_BUTTON_FILLCOLOR));

	CProject7View* pView = (CProject7View*)GetActiveView();
	pView->m_colorFill = pColor->GetColor();
	pView->Invalidate();
}


void CMainFrame::OnCheckPoints()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CMFCRibbonCheckBox* pViewPoint = DYNAMIC_DOWNCAST(CMFCRibbonCheckBox, m_wndRibbonBar.FindByID(ID_CHECK_POINTS));
	
	CProject7View* pView = (CProject7View*)GetActiveView();
	pView->m_bViewPoints = !pView->m_bViewPoints;
	pView->Invalidate();
}


void CMainFrame::OnUpdateCheckPoints(CCmdUI *pCmdUI)
{
	// TODO: 여기에 명령 업데이트 UI 처리기 코드를 추가합니다.
	CProject7View* pView = (CProject7View*)GetActiveView();
	pCmdUI->SetCheck(pView->m_bViewPoints);
}


void CMainFrame::OnEditText()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CMFCRibbonEdit* pText = DYNAMIC_DOWNCAST(CMFCRibbonEdit, m_wndRibbonBar.FindByID(ID_EDIT_TEXT));

	CProject7View* pView = (CProject7View*)GetActiveView();
	pView->m_strText = pText->GetEditText();
	pView->Invalidate();
}




void CMainFrame::OnSpinSize()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CMFCRibbonEdit* pSize = DYNAMIC_DOWNCAST(CMFCRibbonEdit, m_wndRibbonBar.FindByID(ID_SPIN_SIZE));

	CProject7View* pView = (CProject7View*)GetActiveView();
	pView->m_lfFont.lfHeight = (int)_wtoi(pSize->GetEditText());
	pView->Invalidate();
}


void CMainFrame::OnButtonTextcolor()
{
	// TODO: 여기에 명령 처리기 코드를 추가합니다.
	CMFCRibbonColorButton* pColor = DYNAMIC_DOWNCAST(CMFCRibbonColorButton, m_wndRibbonBar.FindByID(ID_BUTTON_TEXTCOLOR));

	CProject7View* pView = (CProject7View*)GetActiveView();
	pView->m_colorText = pColor->GetColor();
	pView->Invalidate();
}
```

```ruby
void CProject7View::DrawEllipse(CDC* pDC)
{
	// TODO: 여기에 구현 코드 추가.
	if (!m_nCount)
		return;

	int x1, y1, x2, y2;
	if (m_nCount < 2)
	{
		x1 = (int)((float)m_ptPoints[0].x * m_fZoom);
		y1 = (int)((float)m_ptPoints[0].y * m_fZoom);
		if (m_bViewPoints)
			pDC->Ellipse(x1 - 4, y1 - 4, x1 + 4, y1 + 4);
	}
	else
	{
		x1 = (int)((float)m_ptPoints[0].x * m_fZoom);
		y1 = (int)((float)m_ptPoints[0].y * m_fZoom);
		x2 = (int)((float)m_ptPoints[1].x * m_fZoom);
		y2 = (int)((float)m_ptPoints[1].y * m_fZoom);
		pDC->Ellipse(x1, y1, x2, y2);

		if (m_bViewPoints)
		{
			pDC->Ellipse(x1 - 4, y1 - 4, x1 + 4, y1 + 4);
			pDC->Ellipse(x2 - 4, y2 - 4, x2 + 4, y2 + 4);
		}
	}
}


void CProject7View::DrawPolygon(CDC* pDC)
{
	// TODO: 여기에 구현 코드 추가.
	CPoint pPoints[20];
	for (int i = 0; i < m_nCount; i++)
	{
		pPoints[i].x = (int)((float)m_ptPoints[i].x * m_fZoom);
		pPoints[i].y = (int)((float)m_ptPoints[i].y * m_fZoom);
	}

	if (m_bDrawing)
		pDC->Polyline(pPoints, m_nCount);
	else
		pDC->Polygon(pPoints, m_nCount);

	if (m_bViewPoints)
	{
		for (int i = 0; i < m_nCount; i++)
			pDC->Ellipse(pPoints[i].x - 4, pPoints[i].y - 4, pPoints[i].x + 4, pPoints[i].y + 4);
	}
}


void CProject7View::ResetZoom()
{
	// TODO: 여기에 구현 코드 추가.
	CMainFrame* pFrame = (CMainFrame*)AfxGetMainWnd();
	CMFCRibbonSlider* pZoom = DYNAMIC_DOWNCAST(CMFCRibbonSlider, pFrame->GetRibbonBar()->FindByID(ID_SLIDER_ZOOM));
	pZoom->SetPos(100);

	m_fZoom = 1.0f;
	Invalidate();
}


void CProject7View::OnLButtonDown(UINT nFlags, CPoint point)
{
	// TODO: 여기에 메시지 처리기 코드를 추가 및/또는 기본값을 호출합니다.
	if (m_nObjectType == 0)
	{
		if (m_nCount == 0 || m_nCount >= 2)
		{
			ResetZoom();
			m_nCount = 0;
			m_bDrawing = true;
		}
		m_ptPoints[m_nCount++] = point;
		if (m_nCount == 2)
			m_bDrawing = false;

		Invalidate();
	}
	else if (m_nObjectType == 1)
	{
		if (!m_bDrawing) 
		{
			ResetZoom();
			m_nCount = 0;
			m_bDrawing = true;
		}
		m_ptPoints[m_nCount++] = point;
		Invalidate();
	}
	m_ptPrev = point;

	CView::OnLButtonDown(nFlags, point);
}


void CProject7View::OnLButtonDblClk(UINT nFlags, CPoint point)
{
	// TODO: 여기에 메시지 처리기 코드를 추가 및/또는 기본값을 호출합니다.
	if (m_nObjectType == 1)
	{
		if (m_bDrawing)
			m_bDrawing = false;

		Invalidate();
	}

	CView::OnLButtonDblClk(nFlags, point);
}


void CProject7View::OnMouseMove(UINT nFlags, CPoint point)
{
	// TODO: 여기에 메시지 처리기 코드를 추가 및/또는 기본값을 호출합니다.
	if (m_nCount < 1 || !m_bDrawing)
		return;

	CClientDC dc(this);
	switch (m_nObjectType)
	{
	case 0:
		DrawEllipse(&dc);
		break;
	case 1:
		DrawPolygon(&dc);
		break;
	}
	dc.SetROP2(R2_NOT);
	dc.MoveTo(m_ptPoints[m_nCount - 1]);
	dc.LineTo(m_ptPrev);

	dc.SetROP2(R2_COPYPEN);
	dc.MoveTo(m_ptPoints[m_nCount - 1]);
	dc.LineTo(point);

	m_ptPrev = point;

	CView::OnMouseMove(nFlags, point);
}
```

