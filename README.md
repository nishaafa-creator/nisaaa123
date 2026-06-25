import streamlit as st
import pandas as pd
import plotly.express as px

# ==========================
# KONFIGURASI HALAMAN
# ==========================
st.set_page_config(
    page_title="Dashboard Penjualan Supermarket",
    page_icon="📊",
    layout="wide"
)

# ==========================
# JUDUL
# ==========================
st.title("📊 Dashboard Penjualan Supermarket")
st.markdown("Dashboard Analisis Data Penjualan")

# ==========================
# UPLOAD FILE
# ==========================
uploaded_file = st.file_uploader(
    "Upload File CSV",
    type=["csv"]
)

if uploaded_file is not None:

    try:
        df = pd.read_csv(uploaded_file)

        st.success("Data berhasil diupload!")

        # ==========================
        # TAMPILKAN DATA
        # ==========================
        st.subheader("📋 Preview Data")

        st.dataframe(
            df.head(),
            use_container_width=True
        )

        # ==========================
        # INFORMASI DATA
        # ==========================
        st.subheader("📌 Informasi Dataset")

        col1, col2, col3 = st.columns(3)

        col1.metric(
            "Jumlah Baris",
            df.shape[0]
        )

        col2.metric(
            "Jumlah Kolom",
            df.shape[1]
        )

        numeric_cols = df.select_dtypes(include="number").columns

        col3.metric(
            "Variabel Numerik",
            len(numeric_cols)
        )

        # ==========================
        # PILIH KOLOM NUMERIK
        # ==========================
        if len(numeric_cols) > 0:

            st.subheader("📊 Visualisasi Statistik")

            selected_col = st.selectbox(
                "Pilih Variabel Numerik",
                numeric_cols
            )

            tab1, tab2, tab3 = st.tabs(
                ["Histogram", "Boxplot", "Statistik"]
            )

            with tab1:

                fig = px.histogram(
                    df,
                    x=selected_col,
                    title=f"Distribusi {selected_col}"
                )

                st.plotly_chart(
                    fig,
                    use_container_width=True
                )

            with tab2:

                fig = px.box(
                    df,
                    y=selected_col,
                    title=f"Boxplot {selected_col}"
                )

                st.plotly_chart(
                    fig,
                    use_container_width=True
                )

            with tab3:

                st.dataframe(
                    df[selected_col]
                    .describe()
                    .to_frame(),
                    use_container_width=True
                )

        # ==========================
        # KOLOM KATEGORI
        # ==========================
        categorical_cols = df.select_dtypes(
            exclude="number"
        ).columns

        if len(categorical_cols) > 0:

            st.subheader("📈 Analisis Kategori")

            selected_cat = st.selectbox(
                "Pilih Variabel Kategori",
                categorical_cols
            )

            count_data = (
                df[selected_cat]
                .value_counts()
                .reset_index()
            )

            count_data.columns = [
                selected_cat,
                "Jumlah"
            ]

            fig = px.bar(
                count_data,
                x=selected_cat,
                y="Jumlah",
                title=f"Jumlah Data Berdasarkan {selected_cat}"
            )

            st.plotly_chart(
                fig,
                use_container_width=True
            )

        # ==========================
        # DATA LENGKAP
        # ==========================
        st.subheader("📑 Data Lengkap")

        st.dataframe(
            df,
            use_container_width=True
        )

    except Exception as e:

        st.error(
            f"Terjadi kesalahan: {e}"
        )

else:

    st.info(
        "Silakan upload file CSV terlebih dahulu."
    )
