![image](https://github.com/user-attachments/assets/ed076aaa-6c82-46de-b00c-e0299fcadaeb)

```ruby
void CProject4Dlg::OnSelchangedTreeControl(NMHDR *pNMHDR, LRESULT *pResult)
{
	LPNMTREEVIEW pNMTreeView = reinterpret_cast<LPNMTREEVIEW>(pNMHDR);
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	m_hSelectedNode = pNMTreeView->itemNew.hItem;

	m_strSeletedNode = m_treeControl.GetItemText(m_hSelectedNode);
	UpdateData(FALSE);
	*pResult = 0;
}


void CProject4Dlg::OnClickedButtonInsert()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	UpdateData(TRUE);
	if (!m_strNodeText.IsEmpty())
	{
		m_treeControl.InsertItem(m_strNodeText, m_hSelectedNode, TVI_LAST);
		m_treeControl.Expand(m_hSelectedNode, TVE_EXPAND);
	}
	else
	{
		AfxMessageBox(_T("입력 노드의 텍스트를 입력하세요."));
	}
	m_strNodeText.Empty();
	UpdateData(FALSE);
}


void CProject4Dlg::OnClickedButtonModify()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	UpdateData(TRUE);
	if (!m_strNodeText.IsEmpty())
	{
		if (m_hSelectedNode != m_hRoot)
		{
			m_treeControl.SetItemText(m_hSelectedNode, m_strNodeText);
			m_strSeletedNode = m_strNodeText;
		}
		else
		{
			AfxMessageBox(_T("루트 노드는 수정해서는 안 됩니다."));
		}
	}
	else
	{
		AfxMessageBox(_T("수정 노드의 텍스트를 입력하세요."));
	}
	m_strNodeText.Empty();
	UpdateData(FALSE);
}


void CProject4Dlg::OnClickedButtonDelete()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	if (m_hSelectedNode != m_hRoot)
	{
		if (MessageBox(_T("정말 삭제하시겠습니까?"), _T("삭제 경고"), MB_YESNO) == IDYES)
		{
			m_treeControl.DeleteItem(m_hSelectedNode);
		}
	}
	else
	{
		AfxMessageBox(_T("루트 노드는 삭제해서는 안 됩니다."));
	}
}


void CProject4Dlg::OnClickedCheckExpand()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	m_bChecked = !m_bChecked;
	m_treeControl.Expand(m_hRoot, TVE_TOGGLE);
	m_hSelectedNode = m_hRoot;
	m_strSeletedNode = _T("루트 노드");
	UpdateData(FALSE);
}
```
