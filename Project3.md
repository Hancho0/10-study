![image](https://github.com/user-attachments/assets/ddd6c8c3-fd73-42f7-a6c8-4edc326fbf7f)
![image](https://github.com/user-attachments/assets/3aa3e89e-3e31-4c94-bf00-2625358e5999)

```ruby
void CProject3Dlg::OnClickedButtonInsert()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	int nCount = m_listStudent.GetItemCount();
	CString strCount;

	UpdateData(TRUE);
	if (!m_strDept.IsEmpty() && !m_strID.IsEmpty() && !m_strName.IsEmpty())
	{
		strCount.Format(_T("%d"), nCount + 1);
		m_listStudent.InsertItem(nCount, strCount);
		m_listStudent.SetItem(nCount, 1, LVIF_TEXT, m_strDept, 0, 0, 0, 0);
		m_listStudent.SetItem(nCount, 2, LVIF_TEXT, m_strID, 0, 0, 0, 0);
		m_listStudent.SetItem(nCount, 3, LVIF_TEXT, m_strName, 0, 0, 0, 0);

		m_strDept.Empty();
		m_strID.Empty();
		m_strName.Empty();

		((CButton*)GetDlgItem(IDC_BUTTON_MODIFY))->EnableWindow(FALSE);
		((CButton*)GetDlgItem(IDC_BUTTON_DELETE))->EnableWindow(FALSE);

		UpdateData(FALSE);
	}
	else
	{
		MessageBox(_T("모든 항목을 입력해 주세요."), _T("잠깐"), MB_OK);
	}
}


void CProject3Dlg::OnItemchangedListStudent(NMHDR *pNMHDR, LRESULT *pResult)
{
	LPNMLISTVIEW pNMLV = reinterpret_cast<LPNMLISTVIEW>(pNMHDR);
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	m_nSelectedItem = pNMLV->iItem;

	m_strSelectedItem.Format(_T("%d"), m_nSelectedItem + 1);

	m_strDept = m_listStudent.GetItemText(m_nSelectedItem, 1);
	m_strID = m_listStudent.GetItemText(m_nSelectedItem, 2);
	m_strName = m_listStudent.GetItemText(m_nSelectedItem, 3);

	((CButton*)GetDlgItem(IDC_BUTTON_MODIFY))->EnableWindow(TRUE);
	((CButton*)GetDlgItem(IDC_BUTTON_DELETE))->EnableWindow(TRUE);

	UpdateData(FALSE);

	*pResult = 0;
}


void CProject3Dlg::OnClickedButtonModify()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	UpdateData(TRUE);
	CString strDept, strID, strName, strIndex;
	strDept = m_strDept;
	strID = m_strID;
	strName = m_strName;

	if (m_nSelectedItem >= 0)
	{
		if (!strDept.IsEmpty() && !strID.IsEmpty() && !strName.IsEmpty())
		{
			strIndex.Format(_T("%d"), m_nSelectedItem + 1);
			m_listStudent.SetItem(m_nSelectedItem, 0, LVIF_TEXT, strIndex, 0, 0, 0, 0);
			m_listStudent.SetItem(m_nSelectedItem, 1, LVIF_TEXT, strDept, 0, 0, 0, 0);
			m_listStudent.SetItem(m_nSelectedItem, 2, LVIF_TEXT, strID, 0, 0, 0, 0);
			m_listStudent.SetItem(m_nSelectedItem, 3, LVIF_TEXT, strName, 0, 0, 0, 0);

			m_strDept.Empty();
			m_strID.Empty();
			m_strName.Empty();

			((CButton*)GetDlgItem(IDC_BUTTON_MODIFY))->EnableWindow(FALSE);
			((CButton*)GetDlgItem(IDC_BUTTON_DELETE))->EnableWindow(FALSE);

			UpdateData(FALSE);

		}
		else
		{
			MessageBox(_T("모든 항목을 입력해 주세요."), _T("잠깐"), MB_OK);
		}
	}
	else
	{
		MessageBox(_T("아이템을 선택하지 않았습니다."), _T("잠깐"), MB_OK);
	}
}


void CProject3Dlg::OnClickedButtonDelete()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	if (m_nSelectedItem >= 0)
	{
		m_listStudent.DeleteItem(m_nSelectedItem);

		for (int i = m_nSelectedItem - 1; i < m_listStudent.GetItemCount(); i++)
		{
			CString strIndex;

			strIndex.Format(_T("%d"), i + 1);
			m_listStudent.SetItemText(i, 0, strIndex);
		}
		m_strDept.Empty();
		m_strID.Empty();
		m_strName.Empty();
		m_strSelectedItem.Empty();

		((CButton*)GetDlgItem(IDC_BUTTON_MODIFY))->EnableWindow(FALSE);
		((CButton*)GetDlgItem(IDC_BUTTON_DELETE))->EnableWindow(FALSE);

		UpdateData(FALSE);
	}
	else
	{
		MessageBox(_T("아이템을 선택하지 않았습니다."), _T("잠깐"), MB_OK);
	}
}


void CProject3Dlg::OnClickedButtonReset()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	m_strDept.Empty();
	m_strID.Empty();
	m_strName.Empty();
	UpdateData(FALSE);
}

void CProject3Dlg::OnSelchangeComboStyle()
{
	// TODO: 여기에 컨트롤 알림 처리기 코드를 추가합니다.
	int numSel = ((CComboBox*)GetDlgItem(IDC_COMBO_STYLE))->GetCurSel();

	switch (numSel)
	{
	case 0:
		m_listStudent.SetView(LV_VIEW_DETAILS);
		break;
	case 1:
		m_listStudent.SetView(LV_VIEW_LIST);
		break;
	case 2:
		m_listStudent.SetView(LV_VIEW_SMALLICON);
		break;
	case 3:
		m_listStudent.SetView(LV_VIEW_ICON);
		break;
	}
}
```
