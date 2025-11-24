PdfPTable detail = new PdfPTable(4)
            {
                WidthPercentage = 100,
                SpacingBefore = 5,
                SpacingAfter = 5
            };
            detail.SetWidths(new float[] { 18, 32, 18, 32 });

            detail.AddCell(Label("Name")); detail.AddCell(Value(empName));
            detail.AddCell(Label("Personal No.")); detail.AddCell(Value(perno));

            detail.AddCell(Label("Department")); detail.AddCell(Value(dept));
            detail.AddCell(Label("Mobile No.")); detail.AddCell(Value(phone));

            detail.AddCell(Label("Location")); detail.AddCell(Value(location));
            detail.AddCell(Label("Guest House")); detail.AddCell(Value(hotel));

            detail.AddCell(Label("Check-in Date")); detail.AddCell(Value(fromdt));
            detail.AddCell(Label("Check-out Date")); detail.AddCell(Value(Todt));

            detail.AddCell(Label("Check-in Time")); detail.AddCell(Value(time.Rows[0]["CheckIn"].ToString()));
            detail.AddCell(Label("Check-out Time")); detail.AddCell(Value(time.Rows[0]["CheckOut"].ToString()));

            detail.AddCell(Label("Duration")); detail.AddCell(Value(Duration));

            doc.Add(detail);
