![image](https://github.com/user-attachments/assets/b6844ece-4437-4ef2-a84f-48fa12043709)

```ruby
#include "pch.h"
#include "Project6.h"
#include "CNCardListBox.h"
#include "Project6Dlg.h"

void CNCardListBox::OnAfterAddItem(int nIndex)
{
	// TODO: 여기에 구현 코드 추가.
	CProject6Dlg* pParent = (CProject6Dlg*)GetParent();
	pParent->AddNameCard(GetItemText(nIndex));
}


void CNCardListBox::OnSelectionChanged()
{
	// TODO: 여기에 구현 코드 추가.
	int nItem;
	if ((nItem = GetSelItem()) < 0)
		return;

	CProject6Dlg* pParent = (CProject6Dlg*)GetParent();
	pParent->SelectNameCard(nItem);
}


BOOL CNCardListBox::OnBeforeRemoveItem(int nIndex)
{
	// TODO: 여기에 구현 코드 추가.
	CProject6Dlg* pParent = (CProject6Dlg*)GetParent();
	pParent->DeleteNameCard(GetSelItem());
	if (pParent->m_nCount == 0)
	{
		pParent->m_pNameCards[0].strCompany.Empty();
		pParent->m_pNameCards[0].strContact.Empty();
		pParent->m_pNameCards[0].strName.Empty();
		pParent->m_pNameCards[0].strTitle.Empty();
		pParent->InitializePropGrid(NameCard());
		pParent->DrawNameCard(NameCard());
	}
	return TRUE;
}
```

```ruby
void CProject6Dlg::AddNameCard(CString strName)
{
	// TODO: 여기에 구현 코드 추가.
	NameCard card;
	card.strName = strName;
	
	m_pNameCards[m_nCount++] = card;
	DrawNameCard(card);
	InitializePropGrid(card);
}


void CProject6Dlg::SelectNameCard(int nIndex)
{
	// TODO: 여기에 구현 코드 추가.
	m_nSelectedCard = nIndex;
	DrawNameCard(m_pNameCards[nIndex]);
	InitializePropGrid(m_pNameCards[nIndex]);
}


void CProject6Dlg::DeleteNameCard(int nIndex)
{
	// TODO: 여기에 구현 코드 추가.
	if (nIndex != (m_nCount - 1))
		memcpy(&m_pNameCards[nIndex], &m_pNameCards[nIndex + 1], sizeof(NameCard) * (m_nCount - nIndex + 1));
	m_nCount--;
}


void CProject6Dlg::InitializePropGrid(NameCard card)
{
	// TODO: 여기에 구현 코드 추가.
	HDITEM item;
	item.cxy = 110;
	item.mask = HDI_WIDTH;
	m_pgCardInfo.GetHeaderCtrl().SetItem(0, &item);

	m_pgCardInfo.RemoveAll();
	m_pgCardInfo.EnableHeaderCtrl(FALSE);
	m_pgCardInfo.SetVSDotNetLook();
	m_pgCardInfo.EnableDescriptionArea();

	CMFCPropertyGridProperty* pGroupInfo = new CMFCPropertyGridProperty(_T("명함 정보"));

	pGroupInfo->AddSubItem(new CMFCPropertyGridProperty(_T("회 사"), card.strCompany, _T("회사 이름을 입력하세요."), 0));

	CMFCPropertyGridProperty* pTitle = new CMFCPropertyGridProperty(_T("직 책"), card.strTitle, _T("직책을 선택하세요."), 1);

	pTitle->AddOption(_T("대표"));
	pTitle->AddOption(_T("부장"));
	pTitle->AddOption(_T("과장"));
	pTitle->AddOption(_T("사원"));
	pTitle->AllowEdit(FALSE);
	pGroupInfo->AddSubItem(pTitle);

	pGroupInfo->AddSubItem(new CMFCPropertyGridProperty(_T("이 름"), card.strName, _T("이름을 입력하세요."), 2));
	pGroupInfo->AddSubItem(new CMFCPropertyGridProperty(_T("연락처"), card.strContact, _T("연락처를 입력하세요."), 3));

	m_pgCardInfo.AddProperty(pGroupInfo);
	m_pgCardInfo.UpdateData(FALSE);
}


void CProject6Dlg::DrawNameCard(NameCard card)
{
	// TODO: 여기에 구현 코드 추가.
	CRect rc;
	CFont font, *pOldFont;
	CDC* pDC = GetDlgItem(IDC_STATIC_NAMECARD)->GetDC();
	GetDlgItem(IDC_STATIC_NAMECARD)->GetClientRect(&rc);

	pDC->FillSolidRect(&rc, RGB(255, 255, 255));

	LOGFONT lf;
	CMFCFontInfo* pInfo = m_fcbText.GetSelFont();
	afxGlobalData.fontRegular.GetLogFont(&lf);

	lf.lfCharSet = pInfo->m_nCharSet;
	lf.lfPitchAndFamily = pInfo->m_nPitchAndFamily;
	lstrcpyn(lf.lfFaceName, pInfo->m_strName, LF_FACESIZE);
	lf.lfHeight = 50;

	font.CreateFontIndirect(&lf);

	pOldFont = pDC->SelectObject(&font);
	pDC->SetTextColor(m_cbtnText.GetColor());
	pDC->TextOut(15, 15, card.strCompany);

	CRect rcTitle(rc);
	rcTitle.right = rc.CenterPoint().x;
	pDC->DrawText(card.strTitle, &rcTitle, DT_RIGHT | DT_VCENTER | DT_SINGLELINE);

	CRect rcName(rc);
	rcName.left = rc.CenterPoint().x;
	pDC->DrawText(card.strName, &rcName, DT_CENTER | DT_VCENTER | DT_SINGLELINE);

	pDC->SelectObject(pOldFont);
	font.DeleteObject();

	lf.lfCharSet = pInfo->m_nCharSet;
	lf.lfPitchAndFamily = pInfo->m_nPitchAndFamily;
	lstrcpyn(lf.lfFaceName, pInfo->m_strName, LF_FACESIZE);
	lf.lfHeight = 25;

	font.CreateFontIndirect(&lf);
	pOldFont = pDC->SelectObject(&font);
	pDC->SetTextColor(m_cbtnText.GetColor());

	CString str;
	CRect rcContact(rc);

	rcContact.right = rcContact.right - 20;
	rcContact.bottom = rcContact.bottom - 20;
	str.Format(_T("연락처 : %s"), card.strContact);
	pDC->DrawText(str, &rcContact, DT_RIGHT | DT_BOTTOM | DT_SINGLELINE);

	pDC->SelectObject(pOldFont);
	font.DeleteObject();
	
}

LRESULT CProject6Dlg::OnPropertyChanged(WPARAM wParam, LPARAM lParam)
{
	CMFCPropertyGridProperty* pProperty = (CMFCPropertyGridProperty*)lParam;

	switch (pProperty->GetData())
	{
	case 0:
		m_pNameCards[m_nSelectedCard].strCompany = pProperty->GetValue();
		break;
	case 1:
		m_pNameCards[m_nSelectedCard].strTitle = pProperty->GetValue();
		break;
	case 2:
		m_pNameCards[m_nSelectedCard].strName = pProperty->GetValue();
		break;
	case 3:
		m_pNameCards[m_nSelectedCard].strContact = pProperty->GetValue();
		break;
	}
	DrawNameCard(m_pNameCards[m_nSelectedCard]);
	return 0L;
};
```
