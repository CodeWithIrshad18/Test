           Section(doc, "ROOM DETAILS");

            PdfPTable room = new PdfPTable(3) { WidthPercentage = 100 };
            room.AddCell(Header("Date"));
            room.AddCell(Header("Choice 1"));
            room.AddCell(Header("Choice 2"));

            foreach (DataRow r in booking.Rows)
            {
                string displayRoom1 = r["ROOM_NO1"] + " - " + GetRoomTypeDescription(
                GetRoomTypeFromOracle(r["GSTHOUSE_ID"].ToString(), r["GSTHOUSE_LOC_ID"].ToString(), r["ROOM_NO1"].ToString()));

                string displayRoom2 = r["ROOM_NO2"] + " - " + GetRoomTypeDescription(
                GetRoomTypeFromOracle(r["GSTHOUSE_ID"].ToString(), r["GSTHOUSE_LOC_ID"].ToString(), r["ROOM_NO2"].ToString()));

                room.AddCell(Convert.ToDateTime(r["BEGDA"]).ToString("MM.dd.yyyy"));
                room.AddCell(displayRoom1);
                room.AddCell(displayRoom2);
            }

            doc.Add(room);



Section(doc, "FAMILY DETAILS");

            PdfPTable fam = new PdfPTable(4) { WidthPercentage = 100 };
            fam.AddCell(Header("Name"));
            fam.AddCell(Header("Relationship"));
            fam.AddCell(Header("Age"));
            fam.AddCell(Header("Gender"));

            DataTable family = GetFamilyDetails(perno, receiptNo);
            int child = 0, adult = 0;

            foreach (DataRow f in family.Rows)
            {
                int age = Convert.ToInt32(f["AGE"]);
                if (age < 18) child++; else adult++;

                fam.AddCell(f["NAME"].ToString());
                fam.AddCell(MapRelation(f["RELATION_CODE"].ToString()));
                fam.AddCell(age.ToString());
                fam.AddCell(MapGender(f["GENDER_CODE"].ToString()));
            }

            doc.Add(fam);
